---
title: 'Snelstart: Digitale inkt herkennen met de Ink Recognizer REST API en Java'
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
ms.openlocfilehash: d2cd4e56477ea39587ce318538c9ddd84c51b03b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448124"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Snelstart: Digitale inkt herkennen met de Ink Recognizer REST API en Java

Gebruik deze snelstart om de Ink Recognizer API te gebruiken voor digitale pennenstreken. Deze Java-toepassing verzendt een API-aanvraag met JSON-geformatteerde inkstrokegegevens en krijgt het antwoord.

Hoewel deze toepassing in Java is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal.

Meestal zou u de API aanroepen vanuit een digitale inktapp. Met deze snelstart worden gegevens over de inktlijn voor het volgende handgeschreven voorbeeld uit een JSON-bestand verzendt.

![een afbeelding van handgeschreven tekst](../media/handwriting-sample.jpg)

De broncode voor deze quickstart is te vinden op [GitHub.](https://go.microsoft.com/fwlink/?linkid=2089904)

## <a name="prerequisites"></a>Vereisten

- De [&trade; Java Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) of hoger.

- Importeer deze bibliotheken uit de Maven Repository
    - [JSON in Java](https://mvnrepository.com/artifact/org.json/json) pakket
    - [Apache HttpClient-pakket](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- De voorbeeldgegevens van de inktlijn voor deze quickstart zijn te vinden op [GitHub.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json)

### <a name="create-an-ink-recognizer-resource"></a>Een inkherkenderbron maken

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Een nieuwe toepassing maken

1. Maak een nieuw Java-project in uw favoriete IDE of editor en importeer de volgende bibliotheken.
    
    [!code-java[import statements](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=imports)]

2. Maak variabelen voor uw abonnementssleutel, eindpunt en JSON-bestand. Het eindpunt wordt later toegevoegd aan de inktherkenninguri.

    [!code-java[initial vars](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Een functie maken om aanvragen te verzenden

1. Maak een nieuwe `sendRequest()` functie genaamd die de hierboven gemaakte variabelen neemt. Voer vervolgens de volgende stappen uit.

2. Maak `CloseableHttpClient` een object dat aanvragen naar de API kan verzenden. Stuur het verzoek `HttpPut` naar een aanvraagobject door uw eindpunt te combineren met de URL van Ink Recognizer.

3. Gebruik de functie `setHeader()` van het `Content-Type` verzoek `application/json`om de koptekst `Ocp-Apim-Subscription-Key` in te stellen op en voeg uw abonnementssleutel toe aan de koptekst.

4. Gebruik de functie `setEntity()` van de aanvraag voor de te sturen gegevens.   

5. Gebruik de functie `execute()` van de client om de `CloseableHttpResponse` aanvraag te verzenden en op te slaan in een object. 

6. Maak `HttpEntity` een object om de antwoordinhoud op te slaan. Krijg de `getEntity()`inhoud met . Als het antwoord niet leeg is, moet u het antwoord retourneren.
    
    [!code-java[send a request](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=sendRequest)]

## <a name="send-an-ink-recognition-request"></a>Een aanvraag voor inktherkenning verzenden

Maak een `recognizeInk()` methode die wordt aangeroepen om uw inktlijngegevens te herkennen. Roep `sendRequest()` de hierboven gemaakte methode aan met uw eindpunt, url, abonnementssleutel en json-gegevens. Haal het resultaat en print het op de console.

[!code-java[recognizeInk](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=recognizeInk)]

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Laad uw digitale inktgegevens en verstuur het verzoek

1. Lees in de hoofdmethode van uw toepassing in het JSON-bestand met de gegevens die aan de aanvragen worden toegevoegd.

2. Roep de hierboven gemaakte inktherkenningsfunctie aan.
    
    [!code-java[main method](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=main)]


## <a name="run-the-application-and-view-the-response"></a>De toepassing uitvoeren en het antwoord weergeven

Voer de toepassing uit. Een geslaagdantwoord wordt geretourneerd in JSON-indeling. U ook de JSON reactie vinden op [GitHub.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [VERWIJZING NAAR REST-API](https://go.microsoft.com/fwlink/?linkid=2089907)


Als u wilt zien hoe de Ink Recognition API werkt in een digitale inktapp, bekijkt u de volgende voorbeeldtoepassingen op GitHub:
* [C# en Universal Windows Platform (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# en Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Webbrowser-app (Javascript)](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobiele app (Java en Android)](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobiele app (Swift en iOS)](https://go.microsoft.com/fwlink/?linkid=2089805)
