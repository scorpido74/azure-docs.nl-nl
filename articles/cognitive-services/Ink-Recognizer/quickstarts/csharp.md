---
title: 'Quickstart: Digitaal handschrift herkennen met de Ink Recognizer-REST API en C#'
titleSuffix: Azure Cognitive Services
description: In deze quickstart zie u hoe u de Ink Recognizer-API en C# kunt gebruiken om digitale pennenstreken te herkennen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: a4bb02f11c7a9a75ddc96e0ee8e9f4b868f8ade5
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051590"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Quickstart: Digitaal handschrift herkennen met de Ink Recognizer-REST API en C#

[!INCLUDE [ink-recognizer-deprecation](../includes/deprecation-note.md)]

Gebruik deze quickstart om te beginnen met digitale pennenstreken naar de Ink Recognizer-API te verzenden. Met deze C#-toepassing wordt een API-aanvraag met gegevens over de pennenstreken met JSON-indeling verstuurd en het antwoord ontvangen.

Hoewel deze toepassing in C# is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal.

Normaal gesproken zou u de API aanroepen vanuit een digitaal handschrift-app. In deze quickstart wordt gegevens over pennenstreken voor het volgende handgeschreven voorbeeld verzonden vanuit een JSON-bestand.

![een afbeelding van handgeschreven tekst](../media/handwriting-sample.jpg)

De broncode voor deze quickstart is te vinden op [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Vereisten

- Elke versie van [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Newtonsoft.json installeren als een NuGet-pakket in Visual Studio:
        1. Klik met de rechtermuisknop op de **Solution Manager**
        2. Klik op **NuGet-pakketten beheren...**
        3. Zoeken naar `Newtonsoft.Json` en het pakket installeren
- Als u Linux/MacOS gebruikt, kan deze toepassing worden uitgevoerd met behulp van [Mono](https://www.mono-project.com/).

- De voorbeeldgegevens over pennenstreken voor deze quickstart vindt u op [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Vision/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Een Ink Recognizer-resource maken

[!INCLUDE [creating-an-ink-recognizer-resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Een nieuwe toepassing maken

1. Maak in Visual Studio een nieuwe consoleoplossing en voeg de volgende pakketten toe. 
    
    [!code-csharp[import statements](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=imports)]

2. Maak variabelen voor uw abonnementssleutel, eindpunt en het JSON-voorbeeldbestand. Het eindpunt zal later worden gecombineerd met `inkRecognitionUrl` om toegang te krijgen tot de API. 

    [!code-csharp[endpoint file and key variables](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Een functie maken voor het verzenden van aanvragen

1. Maak een nieuwe asynchrone functie met de naam `Request` die de hierboven gemaakte variabelen gebruikt.

2. Stel het beveiligingsprotocol en de headergegevens van de client in met behulp van een `HttpClient`-object. Vergeet niet om de abonnementssleutel toe te voegen aan de `Ocp-Apim-Subscription-Key`-header. Maak vervolgens een `StringContent`-object voor de aanvraag.
 
3. Verzend de aanvraag met `PutAsync()`. Als de aanvraag is voltooid, retourneert u het antwoord.  
    
    [!code-csharp[request example method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=request)]

## <a name="send-an-ink-recognition-request"></a>Een aanvraag voor handschriftherkenning verzenden

1. Maak een nieuwe functie met de naam `recognizeInk()`. Stel de aanvraag op en verzend deze door de `Request()`-functie aan te roepen met uw eindpunt, de abonnementssleutel, de URL voor de API en de gegevens van digitale pennenstreken.

2. Deserialiseer het JSON-object en schrijf het naar de console. 
    
    [!code-csharp[request to recognize ink data](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=recognize)]

## <a name="load-your-digital-ink-data"></a>Uw digitale handschriftgegevens laden

Maak een functie met de naam `LoadJson()` om het JSON-bestand met de handschriftgegevens te laden. Gebruik een `StreamReader` en `JsonTextReader` om een `JObject` te maken en het te retourneren.

[!code-csharp[load the JSON file](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=loadJson)]

## <a name="send-the-api-request"></a>De API-aanvraag verzenden

1. In de hoofdmethode van uw toepassing laadt u uw JSON-gegevens met de hierboven gemaakte functie. 

2. Roep de hierboven gemaakte `recognizeInk()`-functie aan. Gebruik `System.Console.ReadKey()` om het consolevenster geopend te houden nadat de toepassing is uitgevoerd.
    
    [!code-csharp[file main method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=main)]


## <a name="run-the-application-and-view-the-response"></a>De toepassing uitvoeren en het antwoord bekijken

Voer de toepassing uit. Er wordt een geslaagd antwoord geretourneerd in JSON-indeling. U kunt ook het JSON-antwoord vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Vision/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naslaginformatie over REST API](https://go.microsoft.com/fwlink/?linkid=2089907)


Bekijk de volgende voorbeeldtoepassingen op GitHub voor meer informatie over de werking van de Ink Recognition-API in een app voor digitaal handschrift:
* [C# en Universal Windows Platform (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# en Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Webbrowser-app (Javascript)](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobiele app (Java en Android)](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobiele app (Swift en iOS)](https://go.microsoft.com/fwlink/?linkid=2089805)
