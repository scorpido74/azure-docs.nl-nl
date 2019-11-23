---
title: 'Quickstart: Create an object detection project with the SDK for C# - Custom Vision'
titleSuffix: Azure Cognitive Services
description: Maak een project, voeg labels toe, upload afbeeldingen, train uw project en detecteer objecten met de .NET SDK met C#.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 08/08/2019
ms.author: areddish
ms.openlocfilehash: 7baa3e2de00997496edb5b445f8426d899ac65a4
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383726"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-net-sdk"></a>Snelstart: een objectdetectieproject maken met de .NET SDK van Custom Vision

Dit artikel biedt informatie en voorbeeldcode om u op weg te helpen met de Custom Vision-SDK voor C# om een objectdetectiemodel te maken. Wanneer u het project hebt gemaakt, kunt u gelabelde regio's toevoegen, afbeeldingen uploaden, het project trainen, de standaardeindpunt-URL voor voorspellingen ophalen en het eindpunt gebruiken om afbeeldingen programmatisch te testen. Gebruik dit voorbeeld als een sjabloon om uw eigen .NET-toepassing te maken. 

## <a name="prerequisites"></a>Vereisten

- Een versie van [Visual Studio 2015 of 2017](https://www.visualstudio.com/downloads/)
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>De Custom Vision-SDK en voorbeeldcode ophalen

Als u een .NET-app wilt schrijven die gebruikmaakt van Custom Vision, hebt u de NuGet-pakketten van Custom Vision nodig. These packages are included in the sample project you will download, but you can access them individually here.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Kloon of download het project [.NET-voorbeelden van Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Ga naar de map **CustomVision/ObjectDetection** en open _ObjectDetection.csproj_ in Visual Studio.

Met dit Visual Studio-project wordt een nieuw Custom Vision-project gemaakt met de naam __My New Project__. Dit kan worden geopend via de [Custom Vision-website](https://customvision.ai/). Vervolgens worden afbeeldingen geüpload om een objectdetectiemodel te trainen en te testen. In dit project wordt het model getraind om vorken en scharen in afbeeldingen te detecteren.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>De code begrijpen

Open het bestand _Program.cs_ en inspecteer de code. [Create environment variables](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) for your training and prediction keys named `CUSTOM_VISION_TRAINING_KEY` and `CUSTOM_VISION_PREDICTION_KEY`, respectively. The script will look for these.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_keys)]

Also, get your Endpoint URL from the Settings page of the Custom Vision website. Save it to an environment variable called `CUSTOM_VISION_ENDPOINT`. The script saves a reference to it at the root of your class.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_endpoint)]

### <a name="create-a-new-custom-vision-service-project"></a>Een nieuw Custom Vision Service-project maken

Met het volgende deel van de code maakt u een objectdetectieproject. Het project wordt weergegeven op de [Custom Vision-website](https://customvision.ai/), die u eerder hebt bezocht. See the [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) method to specify other options when you create your project (explained in the [Build a detector](get-started-build-detector.md) web portal guide).  

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]


### <a name="add-tags-to-the-project"></a>Label aan het project toevoegen

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Afbeeldingen uploaden en labelen

Als u afbeeldingen labelt in objectdetectieprojecten, dient u de regio van elk gelabeld object op te geven met behulp van genormaliseerde coördinaten. Met de volgende code wordt elk voorbeeld van een afbeelding aan de bijbehorende gelabelde regio gekoppeld.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

Vervolgens wordt deze kaart met koppelingen gebruikt om elke voorbeeldafbeelding met de bijbehorende regiocoördinaten te uploaden. You can upload up to 64 images in a single batch.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

Op dit punt aangekomen, zijn alle voorbeeldafbeeldingen geüpload en hebben ze allemaal een label (**vork** of **schaar**) en een bijbehorend, uit pixels bestaande rechthoek voor dat label.

### <a name="train-the-project"></a>Het project trainen

Met deze code wordt de eerste trainingsiteratie in het project gemaakt.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

### <a name="publish-the-current-iteration"></a>Publish the current iteration

The name given to the published iteration can be used to send prediction requests. An iteration is not available in the prediction endpoint until it is published.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]

### <a name="create-a-prediction-endpoint"></a>Een voorspellingseindpunt maken

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction_endpoint)]

### <a name="use-the-prediction-endpoint"></a>Voorspellingseindpunt gebruiken

In dit deel van het script wordt de testafbeelding geladen, wordt een query op het eindpunt van het model uitgevoerd en worden de gegevens van de voorspelling op de console weergegeven.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Terwijl de toepassing wordt uitgevoerd, wordt een consolevenster geopend en wordt de volgende uitvoer geschreven:

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

Vervolgens kunt u controleren of de testafbeelding (gevonden in **Images/Test/** ) op de juiste wijze wordt gelabeld en of de detectieregio juist is. Druk nu op een willekeurige toets om de toepassing af te sluiten.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt nu gezien hoe elke stap van het objectdetectieproces in code kan worden uitgevoerd. Met dit voorbeeld wordt één trainingsinteratie uitgevoerd, maar vaak zult u uw model meerdere keren willen trainen en testen om het nauwkeuriger te maken. In de volgende handleiding wordt classificatie van afbeeldingen behandeld. De principes zijn soortgelijk aan die van objectdetectie.

> [!div class="nextstepaction"]
> [Een model testen en opnieuw trainen](test-your-model.md)
