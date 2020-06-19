---
title: 'Snelstart: een miniatuur maken - REST, JavaScript'
titleSuffix: Azure Cognitive Services
description: In deze snelstart maakt u een miniatuur van een afbeelding met behulp van de Computer Vision-API en JavaScript.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/26/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 94e8901a0526c0e771ad570f4271937a87e83aea
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/18/2020
ms.locfileid: "84986779"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-javascript"></a>Quick Start: een miniatuur genereren met behulp van de Computer Vision REST API en Java script

In deze Quick Start maakt u een miniatuur van een installatie kopie met behulp van de Computer Vision REST API. U geeft de hoogte en breedte op die van de invoer afbeelding kunnen verschillen. Computer Vision maakt gebruik van slim bijsnijden om op intelligente wijze het interessegebied te bepalen en coördinaten voor het bijsnijden te genereren op basis van dat gebied.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/cognitive-services/)
* Wanneer u uw Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" maakt u een computer vision resource Maak "  target="_blank"> een computer vision resource <span class="docon docon-navigate-external x-hidden-focus"></span> </a> in de Azure Portal om uw sleutel en eind punt op te halen. Nadat de app is geïmplementeerd, klikt **u op Ga naar resource**.
    * U hebt de sleutel en het eind punt nodig van de resource die u maakt om de toepassing te verbinden met de Computer Vision-service. U plakt uw sleutel en het eind punt in de onderstaande code verderop in de Quick Start.
    * U kunt de gratis prijs categorie ( `F0` ) gebruiken om de service te proberen en later te upgraden naar een betaalde laag voor productie.

## <a name="create-and-run-the-sample"></a>Het voorbeeld maken en uitvoeren

U kunt het voorbeeld maken en uitvoeren aan de hand van de volgende stappen:

1. Maak een bestand met de naam _get-thumbnail.html_, open het in een tekst editor en kopieer de volgende code hierin.
1. U kunt eventueel de waarde van het `value` kenmerk van het `inputImage` besturings element vervangen door de URL van een andere afbeelding die u wilt analyseren.
1. Open een browservenster.
1. Sleep het bestand in de browser naar het browservenster.
1. Wanneer de webpagina wordt weer gegeven in de browser, plakt u uw abonnements sleutel en eind punt-URL in de relevante invoer vakken.
1. Selecteer ten slotte de knop **miniatuur genereren** .

```html
<!DOCTYPE html>
<html>
<head>
    <title>Thumbnail Sample</title>
</head>
<body>

<script type="text/javascript">
    function processImage() {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        var subscriptionKey = document.getElementById("subscriptionKey").value;
        var endpoint = document.getElementById("endpointUrl").value;
        
        var uriBase = endpoint + "vision/v3.0/generateThumbnail";

        // Request parameters.
        var params = "?width=100&height=150&smartCropping=true";

        // Display the source image.
        var sourceImageUrl = document.getElementById("inputImage").value;
        document.querySelector("#sourceImage").src = sourceImageUrl;

        // Prepare the REST API call:

        // Create the HTTP Request object.
        var xhr = new XMLHttpRequest();

        // Identify the request as a POST, with the URL and parameters.
        xhr.open("POST", uriBase + params);

        // Add the request headers.
        xhr.setRequestHeader("Content-Type","application/json");
        xhr.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

        // Set the response type to "blob" for the thumbnail image data.
        xhr.responseType = "blob";

        // Process the result of the REST API call.
        xhr.onreadystatechange = function(e) {
            if(xhr.readyState === XMLHttpRequest.DONE) {

                // Thumbnail successfully created.
                if (xhr.status === 200) {
                    // Show response headers.
                    var s = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                    document.getElementById("responseTextArea").value =
                        JSON.stringify(xhr.getAllResponseHeaders(), null, 2);

                    // Show thumbnail image.
                    var urlCreator = window.URL || window.webkitURL;
                    var imageUrl = urlCreator.createObjectURL(this.response);
                    document.querySelector("#thumbnailImage").src = imageUrl;
                } else {
                    // Display the error message. The error message is the response
                    // body as a JSON string. The code in this code block extracts
                    // the JSON string from the blob response.
                    var reader = new FileReader();

                    // This event fires after the blob has been read.
                    reader.addEventListener('loadend', (e) => {
                        document.getElementById("responseTextArea").value =
                            JSON.stringify(JSON.parse(e.srcElement.result), null, 2);
                    });

                    // Start reading the blob as text.
                    reader.readAsText(xhr.response);
                }
            }
        }

        // Make the REST API call.
        xhr.send('{"url": ' + '"' + sourceImageUrl + '"}');
    };
</script>

<h1>Generate thumbnail image:</h1>
Enter the URL to an image to use in creating a thumbnail image,
then click the <strong>Generate thumbnail</strong> button.
<br><br>
Subscription key: 
<input type="text" name="subscriptionKey" id="subscriptionKey"
    value="" /> 
Endpoint URL:
<input type="text" name="endpointUrl" id="endpointUrl"
    value="" />
<br><br>
Image for thumbnail:
<input type="text" name="inputImage" id="inputImage"
    value="https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg" />
<button onclick="processImage()">Generate thumbnail</button>
<br><br>
<div id="wrapper" style="width:1160px; display:table;">
    <div id="jsonOutput" style="width:600px; display:table-cell;">
        Response:
        <br><br>
        <textarea id="responseTextArea" class="UIInput"
                  style="width:580px; height:400px;"></textarea>
    </div>
    <div id="imageDiv" style="width:420px; display:table-cell;">
        Source image:
        <br><br>
        <img id="sourceImage" width="400" />
    </div>
    <div id="thumbnailDiv" style="width:140px; display:table-cell;">
        Thumbnail:
        <br><br>
        <img id="thumbnailImage" />
    </div>
</div>
</body>
</html>
```

## <a name="examine-the-response"></a>Het antwoord bekijken

Een geslaagd antwoord wordt geretourneerd als binaire gegevens - deze staan voor de afbeeldingsgegevens van de miniatuur. Als de aanvraag is geslaagd, wordt er een miniatuur gegenereerd op basis van de binaire gegevens in het antwoord. De miniatuur wordt in een browservenster weergegeven. Als de aanvraag mislukt, wordt het antwoord weergegeven in het consolevenster. De reactie op de mislukte aanvraag bevat een foutcode en een bericht om u te helpen bepalen wat er mis is gegaan.

## <a name="next-steps"></a>Volgende stappen

Een JavaScript-toepassing verkennen die Computer Vision gebruikt om optische tekenherkenning (OCR) uit te voeren; slim bijgesneden miniaturen maken; plus visuele kenmerken, inclusief gezichten, in een afbeelding detecteren, categoriseren, labelen en beschrijven. Als u snel wilt experimenteren met de Computer Vision-API, gebruikt u de [Open API-testconsole](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Zelfstudie voor de Computer Vision-API met JavaScript](../Tutorials/javascript-tutorial.md)
