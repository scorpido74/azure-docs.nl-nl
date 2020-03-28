---
title: 'Snelstart: digitale inkt herkennen met de Ink Recognizer REST API en Node.js'
titleSuffix: Azure Cognitive Services
description: Gebruik de Ink Recognizer API om digitale pennenstreken in deze quickstart te herkennen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: a37f2b7044fcba04ca18093aa73563961e9e35de
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448132"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Snelstart: digitale inkt herkennen met de Ink Recognizer REST API en JavaScript

Gebruik deze snelstart om de Ink Recognizer API te gebruiken voor digitale pennenstreken. Met deze JavaScript-toepassing wordt een API-aanvraag uitgevoerd met json-opgemaakte inklijngegevens en wordt het antwoord weergegeven.

Hoewel deze toepassing is geschreven in Javascript en wordt uitgevoerd in uw webbrowser, is de API een RESTful-webservice die compatibel is met de meeste programmeertalen.

Meestal zou u de API aanroepen vanuit een digitale inktapp. Met deze snelstart worden gegevens over de inktlijn voor het volgende handgeschreven voorbeeld uit een JSON-bestand verzendt.

![een afbeelding van handgeschreven tekst](../media/handwriting-sample.jpg)

De broncode voor deze quickstart is te vinden op [GitHub.](https://go.microsoft.com/fwlink/?linkid=2089905)

## <a name="prerequisites"></a>Vereisten

- Een webbrowser
- De voorbeeldgegevens van de inktlijn voor deze quickstart zijn te vinden op [GitHub.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json)

### <a name="create-an-ink-recognizer-resource"></a>Een inkherkenderbron maken

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Een nieuwe toepassing maken

1. Maak in uw favoriete IDE `.html` of editor een nieuw bestand. Voeg er vervolgens basisHTML aan toe voor de code die we later toevoegen.
    
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

2. Voeg `<body>` in de tag de volgende html toe:
    1. Twee tekstgebieden voor het weergeven van de JSON-aanvraag en -respons.
    2. Een knop voor `recognizeInk()` het aanroepen van de functie die later wordt gemaakt.
    
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

## <a name="load-the-example-json-data"></a>Het voorbeeld JSON-gegevens laden

1. Maak `<script>` binnen de tag een variabele voor de sampleJson. Maak vervolgens een JavaScript-functie met de naam `openFile()` die een verkenner opent, zodat u uw JSON-bestand selecteren. Wanneer `Recognize ink` op de knop wordt geklikt, roept deze functie aan en begint het bestand te lezen.
2. Gebruik `FileReader` de functie `onload()` van een object om het bestand asynchroon te verwerken. 
    1. Vervang `\n` een `\r` of andere tekens in het bestand door een lege tekenreeks. 
    2. De `JSON.parse()` tekst converteren naar geldige JSON
    3. Werk `request` het tekstvak in de toepassing bij. De `JSON.stringify()` JSON-tekenreeks opmaken. 
    
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

## <a name="send-a-request-to-the-ink-recognizer-api"></a>Een aanvraag verzenden naar de INK Recognizer API

1. Maak `<script>` binnen de tag `recognizeInk()`een functie met de naam . Deze functie zal later de API aanroepen en de pagina bijwerken met het antwoord. Voeg de code toe uit de volgende stappen binnen deze functie. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. Maak variabelen voor uw URL voor eindpunt, abonnementssleutel en de voorbeeldJSON. Maak vervolgens `XMLHttpRequest` een object om de API-aanvraag te verzenden. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: "https://<your-custom-subdomain>.cognitiveservices.azure.com";
        var SERVER_ADDRESS = process.env["INK_RECOGNITION_ENDPOINT"];
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        var SUBSCRIPTION_KEY = process.env["INK_RECOGNITION_SUBSCRIPTION_KEY"];
        var xhttp = new XMLHttpRequest();
        ```
    2. Maak de retourfunctie `XMLHttpRequest` voor het object. Deze functie ontleedt het API-antwoord van een geslaagde aanvraag en geeft deze weer in de toepassing. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. Maak de foutfunctie voor het aanvraagobject. Met deze functie wordt de fout op de console opgeslagen. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. Maak een functie voor de `onreadystatechange` eigenschap van het aanvraagobject. Wanneer de gereedheidsstatus van het aanvraagobject verandert, worden de bovenstaande retour- en foutfuncties toegepast.
            
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
    
    5. De API-aanvraag verzenden. Voeg uw abonnementssleutel `Ocp-Apim-Subscription-Key` toe aan `content-type` de koptekst en stel de`application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };
        ```

## <a name="run-the-application-and-view-the-response"></a>De toepassing uitvoeren en het antwoord weergeven

Deze toepassing kan worden uitgevoerd in uw webbrowser. Een geslaagdantwoord wordt geretourneerd in JSON-indeling. U ook de JSON reactie vinden op [GitHub:](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [VERWIJZING NAAR REST-API](https://go.microsoft.com/fwlink/?linkid=2089907)

Als u wilt zien hoe de Ink Recognition API werkt in een digitale inktapp, bekijkt u de volgende voorbeeldtoepassingen op GitHub:
* [C# en Universal Windows Platform (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# en Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Webbrowser-app (Javascript)](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobiele app (Java en Android)](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobiele app (Swift en iOS)](https://go.microsoft.com/fwlink/?linkid=2089805)
