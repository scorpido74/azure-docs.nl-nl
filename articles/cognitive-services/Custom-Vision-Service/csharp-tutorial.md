---
title: 'Snelstart: een afbeeldingsclassificatieproject maken met de Custom Vision-SDK voor C#'
titleSuffix: Azure Cognitive Services
description: Maak een project, voeg tags toe, upload afbeeldingen, train uw project en doe een voorspelling met behulp van de .NET-SDK met C#.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: anroth
ms.openlocfilehash: 158e4dcd07f6ba31ad0efdd88f030f8db99fcfdd
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170037"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-net-sdk"></a>Snelstart: een afbeeldingsclassificatieproject maken met de .NET-SDK van Custom Vision

Dit artikel biedt informatie en voorbeeldcode om u op weg te helpen met de Custom Vision-SDK met C# een afbeeldingsclassificatiemodel te maken. Wanneer u het project hebt gemaakt, kunt u tags toevoegen, afbeeldingen uploaden, het project trainen, de standaardeindpunt-URL voor voorspellingen ophalen en het eindpunt gebruiken om afbeeldingen programmatisch te testen. Gebruik dit voor beeld als een sjabloon voor het maken van uw eigen .NET-toepassing. Als u het proces van het bouwen en gebruiken van een classificatiemodel _zonder_ code wilt doorlopen, raadpleegt u in plaats daarvan de [browserrichtlijnen.](getting-started-build-a-classifier.md)

## <a name="prerequisites"></a>Vereisten

- Een versie van [Visual Studio 2015 of 2017](https://www.visualstudio.com/downloads/)
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>De Custom Vision SDK en voorbeeld code ophalen

Als u een .NET-app wilt schrijven die gebruikmaakt van Custom Vision, hebt u de Custom Vision NuGet-pakketten nodig. Deze pakketten zijn opgenomen in het voorbeeld project dat u wilt downloaden, maar u kunt deze afzonderlijk openen.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Kloon of download het project [.NET-voorbeelden van Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Ga naar de map **CustomVision/ImageClassification** en open _ImageClassification.csproj_ in Visual Studio.

Met dit Visual Studio-project wordt een nieuw Custom Vision-project gemaakt met de naam __My New Project__. Dit kan worden geopend via de [Custom Vision-website](https://customvision.ai/). Vervolgens worden afbeeldingen geüpload om een classificatie te trainen en te testen. In dit project moet met de classificatie worden vastgesteld of een boom een __Canadese den__ of een __Japanse sierkers__ is.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>De code begrijpen

Open het bestand _Program.cs_ en inspecteer de code. [Maak omgevingsvariabelen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor uw trainings- `CUSTOM_VISION_TRAINING_KEY` `CUSTOM_VISION_PREDICTION_KEY`en voorspellingssleutels met de naam en , respectievelijk. Het script zal zoeken naar deze variabelen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_keys)]

U kunt ook uw eind punt-URL ophalen via de pagina instellingen van de website van Custom Vision. Sla het bestand op in een omgevings variabele met de naam `CUSTOM_VISION_ENDPOINT`. Er wordt een verwijzing naar het script opgeslagen in de hoofdmap van uw klasse.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_endpoint)]

Met de volgende regels code wordt de primaire functionaliteit van het project uitgevoerd.

### <a name="create-a-new-custom-vision-service-project"></a>Een nieuw Custom Vision Service-project maken

Het project wordt weergegeven op de [Custom Vision-website](https://customvision.ai/), die u eerder hebt bezocht. Zie de methode [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) om andere opties op te geven wanneer u uw project maakt (uitgelegd in de webportalgids [Een classificatie](getting-started-build-a-classifier.md) maken).   

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_create)]

### <a name="create-tags-in-the-project"></a>Labels maken in het project

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Afbeeldingen uploaden en coderen

De afbeeldingen voor dit project zijn bijgevoegd. Er wordt naar verwezen in de methode **LoadImagesFromDisk** in _Program.cs_. U maximaal 64 afbeeldingen uploaden in één batch.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_upload)]

### <a name="train-the-classifier-and-publish"></a>Train de classificatie en publiceer

Deze code maakt de eerste iteratie van het voorspellingsmodel en publiceert die iteratie vervolgens naar het voorspellingseindpunt. U de naam van de iteratie gebruiken om voorspellingsverzoeken te verzenden. Er is geen iteratie beschikbaar in het Voorspellings eindpunt totdat het is gepubliceerd.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_train)]

### <a name="set-the-prediction-endpoint"></a>Voorspellingseindpunt instellen

Het voorspellingseindpunt is de verwijzing waarmee u een afbeelding naar het huidige model kunt verzenden en een classificatievoorspelling kunt verkrijgen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction_endpoint)]

### <a name="submit-an-image-to-the-default-prediction-endpoint"></a>Een afbeelding naar het standaardvoorspellingseindpunt verzenden

In dit script wordt de testafbeelding geladen met de methode **LoadImagesFromDisk** en wordt de voorspellingsuitvoer in de console weergegeven. De waarde `publishedModelName` van de variabele moet overeenkomen met de waarde 'Gepubliceerd als' op het tabblad **Prestaties** van de Aangepaste Visie-portal. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Wanneer de toepassing wordt uitgevoerd, moet deze een console venster openen en de volgende uitvoer schrijven:

```console
Creating new project:
        Uploading images
        Training
Done!

Making a prediction:
        Hemlock: 95.0%
        Japanese Cherry: 0.0%
```

U kunt vervolgens controleren of de testafbeelding (in **Images/Test/**) correct is gelabeld. Druk op een willekeurige toets om de toepassing af te sluiten. U kunt altijd teruggaan naar de [Custom Vision-website](https://customvision.ai) en de huidige status bekijken van het nieuwe project dat u hebt gemaakt.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Volgende stappen

Nu heb je gezien hoe je elke stap van het beeldclassificatieproces in code doen. Met dit voorbeeld wordt één trainingsinteratie uitgevoerd, maar vaak zult u uw model meerdere keren willen trainen en testen om het nauwkeuriger te maken.

> [!div class="nextstepaction"]
> [Een model testen en opnieuw trainen](test-your-model.md)
