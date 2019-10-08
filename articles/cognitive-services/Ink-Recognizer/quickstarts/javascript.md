---
title: 'Quickstart: Digitale inkt herkennen met de inkt Recognizer REST API en node. js'
titleSuffix: Azure Cognitive Services
description: Gebruik de API voor inkt herkenning om te beginnen met het herkennen van digitale inkt streken.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 09/23/2019
ms.author: aahi
ms.openlocfilehash: 19626bd68ad82108b2ebaa823d196d0f22008e29
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996913"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Quickstart: Digitale inkt herkennen met de inkt Recognizer REST API en Java script

Gebruik deze Quick Start om de inkt Recognizer-API te gebruiken voor digitale inkt streken. Deze Java script-toepassing verstuurt een API-aanvraag die is voorzien van inkt lijn gegevens in JSON en geeft het antwoord weer.

Hoewel deze toepassing wordt geschreven in Java script en wordt uitgevoerd in uw webbrowser, is de API een REST-webservice die compatibel is met de meeste programmeer talen.

Normaal gesp roken roept u de API aan vanuit een digitale hand schrift-app. Deze Quick Start verzendt inkt lijn gegevens voor het volgende handgeschreven voor beeld vanuit een JSON-bestand.

![een afbeelding van handgeschreven tekst](../media/handwriting-sample.jpg)

De bron code voor deze snelstartgids vindt u op [github](https://go.microsoft.com/fwlink/?linkid=2089905).

## <a name="prerequisites"></a>Vereisten

- Een webbrowser
- De voorbeeld gegevens voor de inkt lijn voor deze snelstartgids vindt u op [github](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Een bron voor een inkt herkenning maken

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Een nieuwe toepassing maken

1. Maak in uw favoriete IDE of editor een nieuw `.html`-bestand. Voeg vervolgens de Basic-HTML toe aan de code die u later gaat toevoegen.
    
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

2. Voeg binnen de tag `<body>` de volgende HTML toe:
    1. Twee tekst gebieden voor het weer geven van de JSON-aanvraag en het antwoord.
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

## <a name="load-the-example-json-data"></a>De voorbeeld gegevens van JSON laden

1. Maak binnen de tag `<script>` een variabele voor de sampleJson. Maak vervolgens een Java script-functie met de naam `openFile()` waarmee een bestanden Verkenner wordt geopend, zodat u het JSON-bestand kunt selecteren. Wanneer op de knop `Recognize ink` wordt geklikt, wordt deze functie aangeroepen en begint het lezen van het bestand.
2. Gebruik de `onload()`-functie van een @no__t-object om het bestand asynchroon te verwerken. 
    1. Vervang `\n` of `\r` tekens in het bestand door een lege teken reeks. 
    2. @No__t-0 gebruiken om de tekst te converteren naar een geldige JSON
    3. Werk het tekstvak `request` in de toepassing bij. Gebruik `JSON.stringify()` om de JSON-teken reeks op te maken. 
    
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

## <a name="send-a-request-to-the-ink-recognizer-api"></a>Een aanvraag verzenden naar de API voor inkt herkenning

1. Maak binnen de tag `<script>` een functie met de naam `recognizeInk()`. Deze functie roept de API later aan en werkt de pagina bij met het antwoord. Voeg de code uit de volgende stappen in deze functie toe. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. Maak variabelen voor de eind punt-URL, abonnements sleutel en de voor beeld-JSON. Maak vervolgens een `XMLHttpRequest`-object om de API-aanvraag te verzenden. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: "https://<your-custom-subdomain>.cognitiveservices.azure.com";
        var SERVER_ADDRESS = process.env["INK_RECOGNITION_ENDPOINT"];
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        var SUBSCRIPTION_KEY = process.env["INK_RECOGNITION_SUBSCRIPTION_KEY"];
        var xhttp = new XMLHttpRequest();
        ```
    2. Maak de retour functie voor het `XMLHttpRequest`-object. Met deze functie wordt de API-reactie van een geslaagde aanvraag geparseerd en weer gegeven in de toepassing. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. Maak de fout functie voor het object Request. Met deze functie wordt de fout geregistreerd in de-console. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. Maak een functie voor de eigenschap `onreadystatechange` van het object Request. Wanneer de gereedheids status van het aanvraag object wordt gewijzigd, worden de bovenstaande retour-en fout functies toegepast.
            
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
    
    5. De API-aanvraag verzenden. Voeg uw abonnements sleutel toe aan de `Ocp-Apim-Subscription-Key`-header en stel de `content-type` in op `application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };
        ```

## <a name="run-the-application-and-view-the-response"></a>De toepassing uitvoeren en het antwoord weer geven

Deze toepassing kan in uw webbrowser worden uitgevoerd. Een geslaagde reactie wordt geretourneerd in JSON-indeling. U kunt ook het JSON-antwoord vinden op [github](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json):

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naslaginformatie over REST API](https://go.microsoft.com/fwlink/?linkid=2089907)

Bekijk de volgende voorbeeld toepassingen op GitHub voor meer informatie over de werking van de API voor inkt herkenning in een digitale hand schrift-app:
* [C# en Universal Windows Platform (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# en Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Webbrowser-app (Javascript)](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobiele app (Java en Android)](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobiele app (Swift en iOS)](https://go.microsoft.com/fwlink/?linkid=2089805)
