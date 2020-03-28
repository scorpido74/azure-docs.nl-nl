---
title: 'Snelstart: een afbeeldingsclassificatieproject maken met de Custom Vision-SDK voor Java'
titleSuffix: Azure Cognitive Services
description: Maak een project, voeg tags toe, upload afbeeldingen, train uw project en doe een voorspelling met behulp van de Java-SDK.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 02/25/2020
ms.author: areddish
ms.openlocfilehash: a0cfa49dfe247a71e5c2d546e2bb7e9d9b2efc18
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77616351"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-sdk-for-java"></a>Snelstart: een afbeeldingsclassificatieproject maken met de Custom Vision-SDK voor Java

In dit artikel ziet u hoe u aan de slag met de Custom Vision Java SDK om een afbeeldingsclassificatiemodel te maken. Wanneer u het project hebt gemaakt, kunt u tags toevoegen, afbeeldingen uploaden, het project trainen, de standaardeindpunt-URL voor voorspellingen ophalen en het eindpunt gebruiken om afbeeldingen programmatisch te testen. Gebruik dit voorbeeld als een sjabloon om uw eigen Java-toepassing te maken. Zie de [handleiding voor browsers](getting-started-build-a-classifier.md) als u het ontwikkelproces wilt doorlopen en een classificatiemodel wilt gebruiken _zonder_ code.

## <a name="prerequisites"></a>Vereisten

- Een Java IDE naar keuze
- [JDK 7 of 8](https://aka.ms/azure-jdks) is geïnstalleerd.
- [Maven](https://maven.apache.org/) geïnstalleerd
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>De Custom Vision SDK en voorbeeld code ophalen

Als u een Java-app wilt schrijven die gebruikmaakt van Custom Vision, hebt u de Maven-pakketten van Custom Vision nodig. Deze pakketten zijn opgenomen in het voorbeeld project dat u wilt downloaden, maar u kunt deze afzonderlijk openen.

U vindt de Custom Vision SDK in de maven centrale repository:

- [Training-SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [Voorspelling-SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Kloon of download het project [Voorbeelden voor Cognitive Services Java SDK](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). Ga naar de map **Vision/CustomVision/**.

Met dit Java-project wordt een nieuwe afbeeldingsclassificatieproject van Custom Vision gemaakt met de naam __Sample Java Project__. Dit kan worden geopend via de [Custom Vision-website](https://customvision.ai/). Vervolgens worden afbeeldingen geüpload om een classificatie te trainen en te testen. In dit project moet met de classificatie worden vastgesteld of een boom een __Canadese den__ of een __Japanse sierkers__ is.

[!INCLUDE [get-keys](includes/get-keys.md)]

Het programma is geconfigureerd om als omgevingsvariabelen naar uw belangrijkste gegevens te verwijzen. Navigeer naar de map **Vision/CustomVision** en voer de volgende PowerShell-opdrachten in om de omgevingsvariabelen in te stellen. 

> [!NOTE]
> Zie [Omgevingsvariabelen configureren](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows#configure-an-environment-variable-for-authentication) voor instructies als u een niet-Windows-besturingssysteem gebruikt.

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>De code begrijpen

Laad het `Vision/CustomVision`-project in uw Java IDE en open het bestand _CustomVisionSamples.java_. Zoek de **methode runSample** en becommentarieer de **ObjectDetection_Sample** methode die deze methode aanroept,&mdash;voert het objectdetectiescenario uit, dat niet in deze handleiding wordt behandeld. De methode **ImageClassification_Sample** implementeert de primaire functie van dit voorbeeld, namelijk het navigeren naar de definitie ervan en het inspecteren van de code.

### <a name="create-a-custom-vision-service-project"></a>Een Custom Vision Service-project maken

Met het eerste deel van de code wordt een afbeeldingsclassificatieproject gemaakt. Het project wordt weergegeven op de [Custom Vision-website](https://customvision.ai/), die u eerder hebt bezocht. Zie de overbelasting van de [CreateProject-methode](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) om andere opties op te geven wanneer u uw project maakt (uitgelegd in de webportalgids [Een classificatie](getting-started-build-a-classifier.md) maken).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create)]

### <a name="create-tags-in-the-project"></a>Labels maken in het project

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Afbeeldingen uploaden en coderen

De voorbeeldafbeeldingen zijn opgenomen in de map **src/main/resources** van het project. Ze worden daar gelezen en met de bijbehorende labels naar de service geüpload.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload)]

Het vorige codefragment maakt gebruik van twee helperfuncties die de afbeeldingen ophalen als bronstreams en deze uploaden naar de service (u maximaal 64 afbeeldingen in één batch uploaden).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

### <a name="train-the-classifier-and-publish"></a>Train de classificatie en publiceer

Deze code maakt de eerste iteratie van het voorspellingsmodel en publiceert die iteratie vervolgens naar het voorspellingseindpunt. De naam die is opgegeven voor de gepubliceerde herhaling kan worden gebruikt voor het verzenden van voorspellings aanvragen. Een iteratie is pas beschikbaar in het voorspellingseindpunt nadat deze is gepubliceerd.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train)]

### <a name="use-the-prediction-endpoint"></a>Het Voorspellings eindpunt gebruiken

Het voorspellingseindpunt, dat door het `predictor`-object hier wordt weergegeven, is de verwijzing waarmee u een afbeelding naar het huidige model kunt verzenden en een classificatievoorspelling kunt verkrijgen. In dit voorbeeld wordt `predictor` elders gedefinieerd met de omgevingsvariabele voor de voorspellingssleutel.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_predict)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Als u de oplossing wilt compileren en uitvoeren met maven, navigeert u naar de projectmap **(Vision/CustomVision)** in een opdrachtprompt en voert u de opdracht Uitvoeren uit:

```bash
mvn compile exec:java
```

De console-uitvoer van de toepassing moet er uitzien zoals de volgende tekst:

```console
Creating project...
Adding images...
Adding image: hemlock_1.jpg
Adding image: hemlock_2.jpg
Adding image: hemlock_3.jpg
Adding image: hemlock_4.jpg
Adding image: hemlock_5.jpg
Adding image: hemlock_6.jpg
Adding image: hemlock_7.jpg
Adding image: hemlock_8.jpg
Adding image: hemlock_9.jpg
Adding image: hemlock_10.jpg
Adding image: japanese_cherry_1.jpg
Adding image: japanese_cherry_2.jpg
Adding image: japanese_cherry_3.jpg
Adding image: japanese_cherry_4.jpg
Adding image: japanese_cherry_5.jpg
Adding image: japanese_cherry_6.jpg
Adding image: japanese_cherry_7.jpg
Adding image: japanese_cherry_8.jpg
Adding image: japanese_cherry_9.jpg
Adding image: japanese_cherry_10.jpg
Training...
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

U kunt dan controleren of de voorspelling van de tekstafbeelding (de laatste regels van de uitvoer) correct is.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt nu gezien hoe elke stap van het afbeeldingsclassificatieproces in code kan worden uitgevoerd. Met dit voorbeeld wordt één trainingsinteratie uitgevoerd, maar vaak zult u uw model meerdere keren willen trainen en testen om het nauwkeuriger te maken.

> [!div class="nextstepaction"]
> [Een model testen en opnieuw trainen](test-your-model.md)
