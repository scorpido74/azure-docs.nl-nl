---
title: 'Quickstart: Digitale inkt herkennen met de Ink Recognizer-REST API en Java'
titleSuffix: Azure Cognitive Services
description: Gebruik de Ink Recognizer-API om digitale pennenstreken te herkennen in deze quickstart.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 06/30/2020
ms.author: aahi
ms.custom: devx-track-java
ms.openlocfilehash: 902d8a592893a70b8a694ab888bb3c22c6c9670a
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371546"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Quickstart: Digitale inkt herkennen met de Ink Recognizer-REST API en Java

Gebruik deze quickstart om te beginnen met de Ink Recognizer-API te gebruiken voor digitale pennenstreken. Met deze Java-toepassing wordt een API-aanvraag met gegevens over de pennenstreken met JSON-indeling verstuurd en het antwoord ontvangen.

Hoewel deze toepassing in Java is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal.

Normaal gesproken zou u de API aanroepen vanuit een digitaal handschrift-app. In deze quickstart wordt gegevens over pennenstreken voor het volgende handgeschreven voorbeeld verzonden vanuit een JSON-bestand.

![een afbeelding van handgeschreven tekst](../media/handwriting-sample.jpg)

De broncode voor deze quickstart is te vinden op [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="prerequisites"></a>Vereisten

- De [Java&trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) of hoger.

- Deze bibliotheken importeren vanuit de Maven-opslagplaats
    - [JSON in Java](https://mvnrepository.com/artifact/org.json/json)-pakket
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)-pakket

- De voorbeeldgegevens over pennenstreken voor deze quickstart vindt u op [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Een Ink Recognizer-resource maken

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Een nieuwe toepassing maken

1. Maak een nieuw Java-project in uw favoriete IDE of editor en importeer de volgende bibliotheken.
    
    [!code-java[import statements](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=imports)]

2. Maak variabelen voor uw abonnementssleutel, eindpunt en JSON-bestand. Het eindpunt wordt later toegevoegd aan de URI van Ink Recognizer.

    [!code-java[initial vars](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Een functie maken voor het verzenden van aanvragen

1. Maak een nieuwe functie met de naam `sendRequest()` die de hierboven gemaakte variabelen gebruikt. Voer vervolgens de volgende stappen uit.

2. Maak een `CloseableHttpClient`-object waarmee aanvragen kunnen worden verzonden naar de API. Verzend de aanvraag naar een `HttpPut`-aanvraagobject door de combinatie van uw eindpunt en de Ink Recognizer-URL.

3. Stel met behulp van `setHeader()`-functie van de aanvraag de `Content-Type`-header in op `application/json`, en voeg uw abonnementssleutel toe aan de `Ocp-Apim-Subscription-Key`-header.

4. Gebruik de `setEntity()`-functie van de aanvraag voor de te verzenden gegevens.   

5. Gebruik de `execute()`-functie van de client om de aanvraag te verzenden en sla deze op in een `CloseableHttpResponse`-object. 

6. Maak een `HttpEntity`-object voor het opslaan van de inhoud van het antwoord. Haal de inhoud op met `getEntity()`. Als het antwoord niet leeg is, retourneert u het.
    
    [!code-java[send a request](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=sendRequest)]

## <a name="send-an-ink-recognition-request"></a>Een aanvraag voor handschriftherkenning verzenden

Maak een methode met de naam `recognizeInk()` om uw pennenstreekgegevens te herkennen. Roep de hierboven gemaakte `sendRequest()`-methode aan met uw eindpunt, URL, abonnementssleutel en JSON-gegevens. Haal het resultaat op en druk het af naar de console.

[!code-java[recognizeInk](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=recognizeInk)]

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Laad de gegevens van uw digitale handschrift en verzend de aanvraag

1. In de hoofdmethode van uw toepassing kunt u het JSON-bestand inlezen met de gegevens die aan de aanvragen worden toegevoegd.

2. Roep de functie voor handschriftherkenning aan die hierboven is gemaakt.
    
    [!code-java[main method](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=main)]


## <a name="run-the-application-and-view-the-response"></a>De toepassing uitvoeren en het antwoord bekijken

Voer de toepassing uit. Er wordt een geslaagd antwoord geretourneerd in JSON-indeling. U kunt ook het JSON-antwoord vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naslaginformatie over REST API](https://go.microsoft.com/fwlink/?linkid=2089907)


Bekijk de volgende voorbeeldtoepassingen op GitHub voor meer informatie over de werking van de Ink Recognition-API in een app voor digitaal handschrift:
* [C# en Universal Windows Platform (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# en Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Webbrowser-app (Javascript)](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobiele app (Java en Android)](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobiele app (Swift en iOS)](https://go.microsoft.com/fwlink/?linkid=2089805)
