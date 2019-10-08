---
title: 'Quickstart: Digitale inkt herkennen met de inkt Recognizer REST API enC#'
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
ms.openlocfilehash: 0c7d3ed7e2cbaee7d30f368efa004bbb3daaafdd
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996865"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Quickstart: Digitale inkt herkennen met de inkt Recognizer REST API enC#

Gebruik deze Quick Start om digitale inkt streken naar de inkt Recognizer-API te verzenden. Met C# deze toepassing wordt een API-aanvraag verzonden die is voorzien van inkt lijn gegevens in JSON-indeling en wordt het antwoord opgehaald.

Hoewel deze toepassing in C# is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal.

Normaal gesp roken roept u de API aan vanuit een digitale hand schrift-app. Deze Quick Start verzendt inkt lijn gegevens voor het volgende handgeschreven voor beeld vanuit een JSON-bestand.

![een afbeelding van handgeschreven tekst](../media/handwriting-sample.jpg)

De bron code voor deze snelstartgids vindt u op [github](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Vereisten

- Elke versie van [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Newton soft. json installeren als een NuGet-pakket in Visual Studio:
        1. Klik met de rechter muisknop op **Solution Manager**
        2. Klik op **NuGet-pakketten beheren...**
        3. Zoeken naar `Newtonsoft.Json` en het pakket installeren
- Als u Linux/MacOS gebruikt, kan deze toepassing met [mono](https://www.mono-project.com/)worden uitgevoerd.

- De voorbeeld gegevens voor de inkt lijn voor deze snelstartgids vindt u op [github](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Een bron voor een inkt herkenning maken

[!INCLUDE [creating-an-ink-recognizer-resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Een nieuwe toepassing maken

1. Maak in Visual Studio een nieuwe console oplossing en voeg de volgende pakketten toe. 
    
    [!code-csharp[imports](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=imports)]

2. Maak variabelen voor de sleutel en het eind punt van uw abonnement en het JSON-voorbeeld bestand. Het eind punt zal later worden gecombineerd met `inkRecognitionUrl` om toegang te krijgen tot de API. 

    [!code-csharp[endpoint file path and key variables](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Een functie maken om aanvragen te verzenden

1. Maak een nieuwe async-functie met de naam `Request` waarmee de hierboven gemaakte variabelen worden gebruikt.

2. Stel het beveiligings protocol en de header gegevens van de client in met behulp van een `HttpClient`-object. Zorg ervoor dat u uw abonnements sleutel toevoegt aan de `Ocp-Apim-Subscription-Key`-header. Maak vervolgens een `StringContent`-object voor de aanvraag.
 
3. De aanvraag met `PutAsync()` verzenden. Als de aanvraag is voltooid, retourneert u het antwoord.  
    
    [!code-csharp[request example method](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=request)]

## <a name="send-an-ink-recognition-request"></a>Een aanvraag voor inkt herkenning verzenden

1. Maak een nieuwe functie met de naam `recognizeInk()`. Maak de aanvraag en verzend deze door de `Request()`-functie aan te roepen met uw eind punt, abonnements sleutel, de URL voor de API en de gegevens van de digitale inkt lijn.

2. Deserialiseren van het JSON-object en schrijf het naar de-console. 
    
    [!code-csharp[request to recognize ink data](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=recognize)]

## <a name="load-your-digital-ink-data"></a>Uw digitale inkt gegevens laden

Maak een functie met de naam `LoadJson()` om het JSON-bestand met de gegevens van de inkt te laden. Gebruik een `StreamReader` en `JsonTextReader` om een `JObject` te maken en te retour neren.

[!code-csharp[load the JSON file](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=loadJson)]

## <a name="send-the-api-request"></a>De API-aanvraag verzenden

1. In de hoofd methode van uw toepassing laadt u de JSON-gegevens met de functie die hierboven is gemaakt. 

2. Roep de `recognizeInk()`-functie die hierboven is gemaakt aan. Gebruik `System.Console.ReadKey()` om te voor komen dat het console venster wordt geopend nadat de toepassing is uitgevoerd.
    
    [!code-csharp[file main method](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=main)]


## <a name="run-the-application-and-view-the-response"></a>De toepassing uitvoeren en het antwoord weer geven

Voer de toepassing uit. Een geslaagde reactie wordt geretourneerd in JSON-indeling. U kunt ook het JSON-antwoord vinden op [github](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naslaginformatie over REST API](https://go.microsoft.com/fwlink/?linkid=2089907)


Bekijk de volgende voorbeeld toepassingen op GitHub voor meer informatie over de werking van de API voor inkt herkenning in een digitale hand schrift-app:
* [C# en Universal Windows Platform (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# en Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Webbrowser-app (Javascript)](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobiele app (Java en Android)](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobiele app (Swift en iOS)](https://go.microsoft.com/fwlink/?linkid=2089805)
