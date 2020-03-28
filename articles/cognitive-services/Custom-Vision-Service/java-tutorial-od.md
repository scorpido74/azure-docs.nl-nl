---
title: 'Snelstart: een objectdetectieproject maken met de Custom Vision-SDK voor Java'
titleSuffix: Azure Cognitive Services
description: Maak een project, voeg labels toe, upload afbeeldingen, train uw project en detecteer objecten met de Java-SDK.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 02/25/2020
ms.author: areddish
ms.openlocfilehash: 78db95240974d1c9ca07546f8237eca2b564ecb2
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77616331"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-sdk-for-java"></a>Snelstart: een objectdetectieproject maken met de Custom Vision-SDK voor Java

In dit artikel ziet u hoe u aan de slag met de Custom Vision SDK met Java om een objectdetectiemodel te bouwen. Nadat u deze hebt gemaakt, kunt u gelabelde regio's toevoegen, afbeeldingen uploaden, het project trainen, de standaard eind punt-URL van het project ophalen en het eind punt gebruiken om een afbeelding programmatisch te testen. Gebruik dit voorbeeld als een sjabloon om uw eigen Java-toepassing te maken.

## <a name="prerequisites"></a>Vereisten

- Een Java IDE naar keuze
- [JDK 7 of 8](https://aka.ms/azure-jdks) is geïnstalleerd.
- [Maven](https://maven.apache.org/) geïnstalleerd
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>De Custom Vision SDK en voorbeeld code ophalen

Als u een Java-app wilt schrijven die gebruikmaakt van Custom Vision, hebt u de Maven-pakketten van Custom Vision nodig. Deze pakketten zijn opgenomen in het voorbeeldproject dat u downloadt, maar u ze hier individueel openen.

U vindt de Custom Vision SDK in de maven centrale repository:
- [Training-SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [Voorspelling-SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Kloon of download het project [Voorbeelden voor Cognitive Services Java SDK](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). Ga naar de map **Vision/CustomVision/**.

Met dit Java-project wordt een nieuwe objectdetectieproject van Custom Vision gemaakt met de naam __Sample Java OD Project__. Dit kan worden geopend via de [Custom Vision-website](https://customvision.ai/). Vervolgens worden afbeeldingen geüpload om een classificatie te trainen en te testen. In dit project is de classificatie bedoeld om te bepalen of een object een **vork** of **schaar**is.

[!INCLUDE [get-keys](includes/get-keys.md)]

Het programma is geconfigureerd om als omgevingsvariabelen naar uw belangrijkste gegevens te verwijzen. Navigeer naar de map **Vision/CustomVision** en voer de volgende PowerShell-opdrachten in om de omgevingsvariabelen in te stellen. 

> [!NOTE]
> Zie [Omgevingsvariabelen configureren](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows#configure-an-environment-variable-for-authentication) voor instructies als u een niet-Windows-besturingssysteem gebruikt.

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>De code begrijpen

Laad het `Vision/CustomVision`-project in uw Java IDE en open het bestand _CustomVisionSamples.java_. Zoek de **methode runSample** en becommentarieer de **ImageClassification_Sample** methode die&mdash;deze methode aanroept, voert het beeldclassificatiescenario uit, dat niet in deze handleiding wordt behandeld. De methode **ObjectDetection_Sample** implementeert de primaire functie van deze snelstart, namelijk het navigeren naar de definitie ervan en het inspecteren van de code. 

### <a name="create-a-new-custom-vision-service-project"></a>Een nieuw Custom Vision Service project maken

Ga naar het codeblok waarmee een trainingsclient en een objectdetectieproject worden gemaakt. Het project wordt weergegeven op de [Custom Vision-website](https://customvision.ai/), die u eerder hebt bezocht. Zie de overbelasting van de [CreateProject-methode](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) om andere opties op te geven wanneer u uw project maakt (uitgelegd in de webportalgids [Een detector](get-started-build-detector.md) maken).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create_od)]

### <a name="add-tags-to-your-project"></a>Tags toevoegen aan uw project

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags_od)]

### <a name="upload-and-tag-images"></a>Afbeeldingen uploaden en coderen

Wanneer u afbeeldingen in object detectie projecten labelt, moet u de regio van elk gelabeld object opgeven met behulp van genormaliseerde coördinaten. Ga naar de definitie van de kaart `regionMap`. Met deze code wordt elk voorbeeld van een afbeelding aan de bijbehorende gelabelde regio gekoppeld.

> [!NOTE]
> Als u geen gebruik hebt van klikken en slepen om de coördinaten van regio's te markeren, u de webgebruikersinterface op [Customvision.ai](https://www.customvision.ai/)gebruiken. In dit voorbeeld zijn de coördinaten al verstrekt.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_od_mapping)]

Ga vervolgens naar het codeblok waarmee de afbeeldingen aan het project worden toegevoegd. De afbeeldingen worden gelezen vanuit de map **src/main/resources** van het project en worden met de bijbehorende labels en regiocoördinaten naar de service geüpload.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload_od)]

Het vorige codefragment maakt gebruik van twee helperfuncties die de afbeeldingen ophalen als bronstreams en deze uploaden naar de service (u maximaal 64 afbeeldingen in één batch uploaden).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

### <a name="train-the-project-and-publish"></a>Train het project en publiceer

Deze code maakt de eerste iteratie van het voorspellingsmodel en publiceert die iteratie vervolgens naar het voorspellingseindpunt. De naam die is opgegeven voor de gepubliceerde herhaling kan worden gebruikt voor het verzenden van voorspellings aanvragen. Een iteratie is pas beschikbaar in het voorspellingseindpunt nadat deze is gepubliceerd.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train_od)]

### <a name="use-the-prediction-endpoint"></a>Het Voorspellings eindpunt gebruiken

Het voorspellingseindpunt, dat door het `predictor`-object hier wordt weergegeven, is de verwijzing waarmee u een afbeelding naar het huidige model kunt verzenden en een classificatievoorspelling kunt verkrijgen. In dit voorbeeld wordt `predictor` elders gedefinieerd met de omgevingsvariabele voor de voorspellingssleutel.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_prediction_od)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Als u de oplossing wilt compileren en uitvoeren met maven, navigeert u naar de projectmap **(Vision/CustomVision)** in een opdrachtprompt en voert u de opdracht Uitvoeren uit:

```bash
mvn compile exec:java
```

Bekijk de uitvoer in de console voor logboekregistratie en voorspellingsresultaten. Vervolgens kunt u controleren of de testafbeelding op de juiste wijze wordt gelabeld en of de detectieregio juist is.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt nu gezien hoe elke stap van het objectdetectieproces in code kan worden uitgevoerd. Met dit voorbeeld wordt één trainingsinteratie uitgevoerd, maar vaak zult u uw model meerdere keren willen trainen en testen om het nauwkeuriger te maken. In de volgende handleiding wordt classificatie van afbeeldingen behandeld. De principes zijn soortgelijk aan die van objectdetectie.

> [!div class="nextstepaction"]
> [Een model testen en opnieuw trainen](test-your-model.md)
