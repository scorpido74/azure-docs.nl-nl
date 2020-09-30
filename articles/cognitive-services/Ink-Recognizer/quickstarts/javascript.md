---
title: 'Quickstart: Digitaal handschrift herkennen met de Ink Recognizer-REST API en Node.js'
titleSuffix: Azure Cognitive Services
description: Gebruik de Ink Recognizer-API en JavaScript om digitale pennenstreken te herkennen in deze quickstart.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 13e094b0f5d59e070a96ab4b45dcd37315c46c60
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327347"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Quickstart: Digitale inkt herkennen met de Ink Recognizer-REST API en JavaScript

[!INCLUDE [ink-recognizer-deprecation](../includes/deprecation-note.md)]

Gebruik deze quickstart om te beginnen met de Ink Recognizer-API te gebruiken voor digitale pennenstreken. Met deze JavaScript-toepassing wordt een API-aanvraag met gegevens over de pennenstreken met JSON-indeling verstuurd en het antwoord weergegeven.

Hoewel deze toepassing in JavaScript is geschreven en in uw webbrowser wordt uitgevoerd, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal.

Normaal gesproken zou u de API aanroepen vanuit een digitaal handschrift-app. In deze quickstart wordt gegevens over pennenstreken voor het volgende handgeschreven voorbeeld verzonden vanuit een JSON-bestand.

![een afbeelding van handgeschreven tekst](../media/handwriting-sample.jpg)

De broncode voor deze quickstart is te vinden op [GitHub](https://go.microsoft.com/fwlink/?linkid=2089905).

## <a name="prerequisites"></a>Vereisten

- Een webbrowser
- De voorbeeldgegevens over pennenstreken voor deze quickstart vindt u op [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Een Ink Recognizer-resource maken

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Een nieuwe toepassing maken

1. Maak een nieuw `.html`-bestand in uw favoriete IDE of editor. Voeg vervolgens basis-HTML toe aan de code die u later gaat toevoegen.
    
    ```html
    <!DOCTYPE html>
    <html>
    
        <head>
            <script type="text/javascript">
            </script>
        </head>
        
        <body>
        </body>
    
    </html>
    ```

2. Voeg binnen de `<body>`-tag de volgende HTML toe:
    1. Twee tekstgebieden voor het weergeven van de JSON-aanvraag en het antwoord.
    2. Een knop voor het aanroepen van de `recognizeInk()`-functie die later wordt gemaakt.
    
    ```HTML
    <!-- <body>-->
        <h2>Send a request to the Ink Recognition API</h2>
        <p>Request:</p>
        <textarea id="request" style="width:800px;height:300px"></textarea>
        <p>Response:</p>
        <textarea id="response" style="width:800px;height:300px"></textarea>
        <br>
        <button type="button" onclick="recognizeInk()">Recognize Ink</button>
    <!--</body>-->
    ```

## <a name="load-the-example-json-data"></a>De JSON-voorbeeldgegevens laden

1. Maak in de `<script>`-tag een variabele voor de sampleJson. Maak vervolgens een JavaScript-functie met de naam `openFile()` waarmee een bestandenverkenner wordt geopend, zodat u uw JSON-bestand kunt selecteren. Wanneer op de knop `Recognize ink` wordt geklikt, wordt deze functie aangeroepen en begint het lezen van het bestand.
2. Gebruik de `onload()`-functie van een `FileReader`-object om het bestand asynchroon te verwerken. 
    1. Vervang `\n`- of `\r`-tekens in het bestand door een lege tekenreeks. 
    2. `JSON.parse()` gebruiken om de tekst te converteren naar geldige JSON
    3. Werk het `request`-tekstvak in de toepassing bij. Gebruik `JSON.stringify()` om de JSON-tekenreeks op te maken. 
    
    ```javascript
    var sampleJson = "";
    function openFile(event) {
        var input = event.target;
    
        var reader = new FileReader();
        reader.onload = function(){
            sampleJson = reader.result.replace(/(\\r\\n|\\n|\\r)/gm, "");
            sampleJson = JSON.parse(sampleJson);
            document.getElementById('request').innerHTML = JSON.stringify(sampleJson, null, 2);
        };
        reader.readAsText(input.files[0]);
    };
    ```

## <a name="send-a-request-to-the-ink-recognizer-api"></a>Een aanvraag naar de Ink Recognizer-API verzenden

1. Maak in de `<script>`-tag een functie met de naam `recognizeInk()`. Met deze functie wordt de API later aangeroepen en de pagina bijgewerkt met het antwoord. Voeg binnen deze functie de code toe uit de volgende stappen. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. Maak variabelen voor uw eindpunt-URL, abonnementssleutel en voorbeeld-JSON. Maak vervolgens een `XMLHttpRequest`-object om de API-aanvraag te verzenden. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: "https://<your-custom-subdomain>.cognitiveservices.azure.com";
        var SERVER_ADDRESS = process.env["INK_RECOGNITION_ENDPOINT"];
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        var SUBSCRIPTION_KEY = process.env["INK_RECOGNITION_SUBSCRIPTION_KEY"];
        var xhttp = new XMLHttpRequest();
        ```
    2. Maak de retourneerfunctie voor het `XMLHttpRequest`-object. Met deze functie wordt het API-antwoord van een geslaagde aanvraag geparseerd en weergegeven in de toepassing. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. Maak de foutfunctie voor het aanvraagobject. Met deze functie wordt de fout geregistreerd in de console. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. Maak een functie voor de `onreadystatechange`-eigenschap van het aanvraagobject. Wanneer de gereedheidsstatus van het aanvraagobject wordt gewijzigd, worden de bovenstaande retourneer- en foutfuncties toegepast.
            
        ```javascript
        xhttp.onreadystatechange = function () {
            if (this.readyState === 4) {
                if (this.status === 200) {
                    returnFunction(xhttp);
                } else {
                    errorFunction(xhttp);
                }
            }
        };
        ```
    
    5. Verzend de API-aanvraag. Uw abonnementssleutel toevoegen aan de `Ocp-Apim-Subscription-Key`-header, en de `content-type` instellen op `application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };
        ```

## <a name="run-the-application-and-view-the-response"></a>De toepassing uitvoeren en het antwoord bekijken

Deze toepassing kan in uw webbrowser worden uitgevoerd. Er wordt een geslaagd antwoord geretourneerd in JSON-indeling. U kunt ook het JSON-antwoord vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json):

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naslaginformatie over REST API](https://go.microsoft.com/fwlink/?linkid=2089907)

Bekijk de volgende voorbeeldtoepassingen op GitHub voor meer informatie over de werking van de Ink Recognition-API in een app voor digitaal handschrift:
* [C# en Universal Windows Platform (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# en Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Webbrowser-app (Javascript)](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobiele app (Java en Android)](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobiele app (Swift en iOS)](https://go.microsoft.com/fwlink/?linkid=2089805)
