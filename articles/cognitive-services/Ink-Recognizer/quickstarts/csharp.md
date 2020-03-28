---
title: 'Snelstart: digitale inkt herkennen met de Ink Recognizer REST API en C #'
titleSuffix: Azure Cognitive Services
description: Deze quickstart laat zien hoe u de Ink Recognizer API gebruiken om digitale pennenstreken te herkennen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: c24d055f1904453d2f512a278f00e23c6fea1d9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371384"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Snelstart: digitale inkt herkennen met de Ink Recognizer REST API en C #

Gebruik deze snelstart om te beginnen met het verzenden van digitale pennenstreken naar de Ink Recognizer API. Deze C#-toepassing verzendt een API-aanvraag met JSON-opgemaakte inklijngegevens en krijgt het antwoord.

Hoewel deze toepassing in C# is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal.

Meestal zou u de API aanroepen vanuit een digitale inktapp. Met deze snelstart worden gegevens over de inktlijn voor het volgende handgeschreven voorbeeld uit een JSON-bestand verzendt.

![een afbeelding van handgeschreven tekst](../media/handwriting-sample.jpg)

De broncode voor deze quickstart is te vinden op [GitHub.](https://go.microsoft.com/fwlink/?linkid=2089502)

## <a name="prerequisites"></a>Vereisten

- Eender welke versie van [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Ga als lid van de studio naar Newtonsoft.Json als NuGet-pakket:
        1. Klik met de rechtermuisknop op **Solution Manager**
        2. Klik **op NuGet-pakketten beheren...**
        3. Het `Newtonsoft.Json` pakket zoeken en installeren
- Als u Linux/MacOS gebruikt, kan deze toepassing worden uitgevoerd met [Mono](https://www.mono-project.com/).

- De voorbeeldgegevens van de inktlijn voor deze quickstart zijn te vinden op [GitHub.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Vision/InkRecognition/quickstart/example-ink-strokes.json)

### <a name="create-an-ink-recognizer-resource"></a>Een inkherkenderbron maken

[!INCLUDE [creating-an-ink-recognizer-resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Een nieuwe toepassing maken

1. Maak in Visual Studio een nieuwe consoleoplossing en voeg de volgende pakketten toe. 
    
    [!code-csharp[import statements](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=imports)]

2. Maak variabelen voor uw abonnementssleutel en eindpunt en het voorbeeld-JSON-bestand. Het eindpunt wordt later `inkRecognitionUrl` gecombineerd met toegang tot de API. 

    [!code-csharp[endpoint file and key variables](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Een functie maken om aanvragen te verzenden

1. Maak een nieuwe async-functie genaamd `Request` die de hierboven gemaakte variabelen neemt.

2. Stel het beveiligingsprotocol en de headergegevens van de client in met behulp van een `HttpClient` object. Zorg ervoor dat u uw `Ocp-Apim-Subscription-Key` abonnementssleutel toevoegt aan de koptekst. Maak vervolgens `StringContent` een object voor de aanvraag.
 
3. Stuur het `PutAsync()`verzoek met . Als de aanvraag is geslaagd, voert u het antwoord terug.  
    
    [!code-csharp[request example method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=request)]

## <a name="send-an-ink-recognition-request"></a>Een aanvraag voor inktherkenning verzenden

1. Een nieuwe functie `recognizeInk()`maken, genaamd . Bouw de aanvraag en verzend `Request()` deze door de functie aan te roepen met uw eindpunt, abonnementssleutel, de URL voor de API en de gegevens over de digitale pennenstreek.

2. Deserialiseren van het JSON-object en schrijf het naar de console. 
    
    [!code-csharp[request to recognize ink data](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=recognize)]

## <a name="load-your-digital-ink-data"></a>Uw digitale inktgegevens laden

Maak een `LoadJson()` functie aangeroepen om het JSON-bestand voor inktgegevens te laden. Gebruik `StreamReader` een `JsonTextReader` en `JObject` om een te maken en terug te keren.

[!code-csharp[load the JSON file](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=loadJson)]

## <a name="send-the-api-request"></a>De API-aanvraag verzenden

1. Laad in de hoofdmethode van uw toepassing uw JSON-gegevens met de hierboven gemaakte functie. 

2. Roep `recognizeInk()` de functie hierboven aan. Met `System.Console.ReadKey()` deze versie moet u het consolevenster open houden nadat de toepassing is uitgevoerd.
    
    [!code-csharp[file main method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=main)]


## <a name="run-the-application-and-view-the-response"></a>De toepassing uitvoeren en het antwoord weergeven

Voer de toepassing uit. Een geslaagdantwoord wordt geretourneerd in JSON-indeling. U ook de JSON reactie vinden op [GitHub.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Vision/InkRecognition/quickstart/example-response.json)


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [VERWIJZING NAAR REST-API](https://go.microsoft.com/fwlink/?linkid=2089907)


Als u wilt zien hoe de Ink Recognition API werkt in een digitale inktapp, bekijkt u de volgende voorbeeldtoepassingen op GitHub:
* [C# en Universal Windows Platform (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# en Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Webbrowser-app (Javascript)](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobiele app (Java en Android)](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobiele app (Swift en iOS)](https://go.microsoft.com/fwlink/?linkid=2089805)
