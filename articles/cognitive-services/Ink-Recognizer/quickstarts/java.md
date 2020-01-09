---
title: 'Snelstartgids: digitale inkt herkennen met de inkt Recognizer REST API en Java'
titleSuffix: Azure Cognitive Services
description: Gebruik de API voor inkt herkenning om digitale inkt streken in deze Quick Start te herkennen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: d2cd4e56477ea39587ce318538c9ddd84c51b03b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448124"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Snelstartgids: digitale inkt herkennen met de inkt Recognizer REST API en Java

Gebruik deze Quick Start om de inkt Recognizer-API te gebruiken voor digitale inkt streken. Deze Java-toepassing verstuurt een API-aanvraag met gegevens over de JSON-indeling en ontvangt de reactie.

Hoewel deze toepassing in Java is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal.

Normaal gesp roken roept u de API aan vanuit een digitale hand schrift-app. Deze Quick Start verzendt inkt lijn gegevens voor het volgende handgeschreven voor beeld vanuit een JSON-bestand.

![een afbeelding van handgeschreven tekst](../media/handwriting-sample.jpg)

De bron code voor deze snelstartgids vindt u op [github](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="prerequisites"></a>Vereisten

- De [Java&trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) of hoger.

- Deze bibliotheken importeren vanuit de Maven-opslag plaats
    - [Json in Java](https://mvnrepository.com/artifact/org.json/json) -pakket
    - [Apache httpclient maakt](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) -pakket

- De voorbeeld gegevens voor de inkt lijn voor deze snelstartgids vindt u op [github](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Een bron voor een inkt herkenning maken

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Een nieuwe toepassing maken

1. Maak een nieuw Java-project in uw favoriete IDE of editor en importeer de volgende bibliotheken.
    
    [!code-java[import statements](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=imports)]

2. Maak variabelen voor uw abonnements sleutel, eind punt en JSON-bestand. Het eind punt wordt later toegevoegd aan de URI van de inkt herkenning.

    [!code-java[initial vars](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Een functie maken om aanvragen te verzenden

1. Maak een nieuwe functie met de naam `sendRequest()` die de hierboven gemaakte variabelen gebruikt. Voer vervolgens de volgende stappen uit.

2. Maak een `CloseableHttpClient`-object waarmee aanvragen kunnen worden verzonden naar de API. De aanvraag verzenden naar een `HttpPut`-aanvraag object door uw eind punt te combi neren en de URL voor de Handschrift herkenning.

3. Gebruik de functie `setHeader()` van de aanvraag om de `Content-Type`-header in te stellen op `application/json`en voeg uw abonnements sleutel toe aan de `Ocp-Apim-Subscription-Key`-header.

4. Gebruik de `setEntity()` functie van de aanvraag voor de gegevens die moeten worden verzonden.   

5. Gebruik de `execute()` functie van de client om de aanvraag te verzenden en op te slaan in een `CloseableHttpResponse`-object. 

6. Maak een `HttpEntity`-object om de antwoord inhoud op te slaan. Haal de inhoud op met `getEntity()`. Als het antwoord niet leeg is, retourneert u het.
    
    [!code-java[send a request](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=sendRequest)]

## <a name="send-an-ink-recognition-request"></a>Een aanvraag voor inkt herkenning verzenden

Maak een methode met de naam `recognizeInk()` om uw pennen streek gegevens te herkennen. Roep de hierboven gemaakte `sendRequest()` methode aan met uw eind punt, URL, abonnements sleutel en JSON-gegevens. Bekijk het resultaat en druk het af op de-console.

[!code-java[recognizeInk](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=recognizeInk)]

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Laad de gegevens van uw digitale inkt en verzend de aanvraag

1. In de hoofd methode van uw toepassing leest u in het JSON-bestand dat de gegevens bevat die aan de aanvragen worden toegevoegd.

2. Roep de functie voor inkt herkenning aan die hierboven is gemaakt.
    
    [!code-java[main method](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=main)]


## <a name="run-the-application-and-view-the-response"></a>De toepassing uitvoeren en het antwoord weer geven

Voer de toepassing uit. Een geslaagde reactie wordt geretourneerd in JSON-indeling. U kunt ook het JSON-antwoord vinden op [github](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naslaginformatie over REST API](https://go.microsoft.com/fwlink/?linkid=2089907)


Bekijk de volgende voorbeeld toepassingen op GitHub voor meer informatie over de werking van de API voor inkt herkenning in een digitale hand schrift-app:
* [C# en Universal Windows Platform (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# en Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Webbrowser-app (Javascript)](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobiele app (Java en Android)](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobiele app (Swift en iOS)](https://go.microsoft.com/fwlink/?linkid=2089805)
