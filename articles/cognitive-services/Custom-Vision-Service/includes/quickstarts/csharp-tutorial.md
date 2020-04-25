---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 04/14/2020
ms.openlocfilehash: de0c8fd674872a48a302a76a3f0951df13041206
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82134108"
---
Dit artikel biedt informatie en voorbeeldcode om u op weg te helpen met de Custom Vision-SDK met C# een afbeeldingsclassificatiemodel te maken. Wanneer u het project hebt gemaakt, kunt u tags toevoegen, afbeeldingen uploaden, het project trainen, de standaardeindpunt-URL voor voorspellingen ophalen en het eindpunt gebruiken om afbeeldingen programmatisch te testen. Gebruik dit voor beeld als een sjabloon voor het maken van uw eigen .NET-toepassing. Als u het proces van het maken en gebruiken van een classificatie model _zonder_ code wilt door lopen, raadpleegt u in plaats daarvan de [op browser gebaseerde richt lijnen](../../getting-started-build-a-classifier.md) .

## <a name="prerequisites"></a>Vereisten

- Een versie van [Visual Studio 2015 of 2017](https://www.visualstudio.com/downloads/)
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>De Custom Vision SDK en voorbeeld code ophalen

Als u een .NET-app wilt schrijven die gebruikmaakt van Custom Vision, hebt u de Custom Vision NuGet-pakketten nodig. Deze pakketten zijn opgenomen in het voorbeeld project dat u wilt downloaden, maar u kunt deze afzonderlijk openen.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Kloon of download het project [.NET-voorbeelden van Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Ga naar de map **CustomVision/ImageClassification** en open _ImageClassification.csproj_ in Visual Studio.

Met dit Visual Studio-project wordt een nieuw Custom Vision-project gemaakt met de naam __My New Project__. Dit kan worden geopend via de [Custom Vision-website](https://customvision.ai/). Vervolgens worden afbeeldingen geüpload om een classificatie te trainen en te testen. In dit project moet met de classificatie worden vastgesteld of een boom een __Canadese den__ of een __Japanse sierkers__ is.

[!INCLUDE [get-keys](../../includes/get-keys.md)]

## <a name="understand-the-code"></a>De code begrijpen

Open het bestand _Program.cs_ en inspecteer de code. [Omgevings variabelen maken](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor uw trainings-en Voorspellings `CUSTOM_VISION_TRAINING_KEY` sleutels `CUSTOM_VISION_PREDICTION_KEY`met de naam en. Het script zoekt naar deze variabelen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_keys)]

U kunt ook uw eind punt-URL ophalen via de pagina instellingen van de website van Custom Vision. Sla het bestand op in een omgevings variabele met de naam `CUSTOM_VISION_ENDPOINT`. Er wordt een verwijzing naar het script opgeslagen in de hoofdmap van uw klasse.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_endpoint)]

Met de volgende regels code wordt de primaire functionaliteit van het project uitgevoerd.

### <a name="create-a-new-custom-vision-service-project"></a>Een nieuw Custom Vision Service-project maken

Het project wordt weergegeven op de [Custom Vision-website](https://customvision.ai/), die u eerder hebt bezocht. Raadpleeg de [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) -methode om andere opties op te geven wanneer u uw project maakt (Zie de hand leiding [een classificatie](../../getting-started-build-a-classifier.md) -webportal bouwen).   

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_create)]

### <a name="create-tags-in-the-project"></a>Labels maken in het project

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Afbeeldingen uploaden en coderen

De afbeeldingen voor dit project zijn bijgevoegd. Er wordt naar verwezen in de methode **LoadImagesFromDisk** in _Program.cs_. U kunt Maxi maal 64 installatie kopieën uploaden in één batch.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_upload)]

### <a name="train-the-classifier-and-publish"></a>De classificatie trainen en publiceren

Deze code maakt de eerste iteratie van het Voorspellings model en publiceert die iteratie vervolgens naar het Voorspellings eindpunt. U kunt de naam van de iteratie gebruiken om Voorspellings aanvragen te verzenden. Er is geen iteratie beschikbaar in het Voorspellings eindpunt totdat het is gepubliceerd.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_train)]

### <a name="set-the-prediction-endpoint"></a>Voorspellingseindpunt instellen

Het voorspellingseindpunt is de verwijzing waarmee u een afbeelding naar het huidige model kunt verzenden en een classificatievoorspelling kunt verkrijgen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction_endpoint)]

### <a name="submit-an-image-to-the-default-prediction-endpoint"></a>Een afbeelding naar het standaardvoorspellingseindpunt verzenden

In dit script wordt de testafbeelding geladen met de methode **LoadImagesFromDisk** en wordt de voorspellingsuitvoer in de console weergegeven. De waarde van de `publishedModelName` variabele moet overeenkomen met de waarde ' gepubliceerd als ' op het tabblad **prestaties** van de Custom Vision Portal. 

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

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt nu gezien hoe u elke stap van het proces voor het classificeren van afbeeldingen in code kunt uitvoeren. Met dit voorbeeld wordt één trainingsinteratie uitgevoerd, maar vaak zult u uw model meerdere keren willen trainen en testen om het nauwkeuriger te maken.

> [!div class="nextstepaction"]
> [Een model testen en opnieuw trainen](../../test-your-model.md)
