---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 04/14/2020
ms.openlocfilehash: efbfed5198620fc07b10354a5b74f6ddaf2cd4ab
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82134136"
---
In dit artikel leest u hoe u aan de slag kunt met de Custom Vision Java SDK om een installatie kopie classificatie model te bouwen. Wanneer u het project hebt gemaakt, kunt u tags toevoegen, afbeeldingen uploaden, het project trainen, de standaardeindpunt-URL voor voorspellingen ophalen en het eindpunt gebruiken om afbeeldingen programmatisch te testen. Gebruik dit voorbeeld als een sjabloon om uw eigen Java-toepassing te maken. Zie de [handleiding voor browsers](../../getting-started-build-a-classifier.md) als u het ontwikkelproces wilt doorlopen en een classificatiemodel wilt gebruiken _zonder_ code.

## <a name="prerequisites"></a>Vereisten

- Een Java IDE naar keuze
- [JDK 7 of 8](https://aka.ms/azure-jdks) is geïnstalleerd.
- [Maven](https://maven.apache.org/) geïnstalleerd
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>De Custom Vision SDK en voorbeeld code ophalen

Als u een Java-app wilt schrijven die gebruikmaakt van Custom Vision, hebt u de Maven-pakketten van Custom Vision nodig. Deze pakketten zijn opgenomen in het voorbeeld project dat u wilt downloaden, maar u kunt deze afzonderlijk openen.

U vindt de Custom Vision SDK in de centrale opslag plaats maven:

- [Training-SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [Voorspelling-SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Kloon of download het project [Voorbeelden voor Cognitive Services Java SDK](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). Ga naar de map **Vision/CustomVision/**.

Met dit Java-project wordt een nieuwe afbeeldingsclassificatieproject van Custom Vision gemaakt met de naam __Sample Java Project__. Dit kan worden geopend via de [Custom Vision-website](https://customvision.ai/). Vervolgens worden afbeeldingen geüpload om een classificatie te trainen en te testen. In dit project moet met de classificatie worden vastgesteld of een boom een __Canadese den__ of een __Japanse sierkers__ is.

[!INCLUDE [get-keys](../../includes/get-keys.md)]

Het programma is geconfigureerd om te verwijzen naar de belangrijkste gegevens als omgevings variabelen. Ga naar de map **Vision/CustomVision** en voer de volgende Power shell-opdrachten in om de omgevings variabelen in te stellen. 

> [!NOTE]
> Als u een niet-Windows-besturings systeem gebruikt, raadpleegt u [omgevings variabelen configureren](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows#configure-an-environment-variable-for-authentication) voor instructies.

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>De code begrijpen

Laad het `Vision/CustomVision`-project in uw Java IDE en open het bestand _CustomVisionSamples.java_. Zoek de methode **runSample** en voer een opmerking uit bij de&mdash;aanroep van de **ObjectDetection_Sample** methode deze methode voert het object detectie scenario uit dat niet in deze hand leiding wordt besproken. De methode **ImageClassification_Sample** implementeert de primaire functie van dit voorbeeld, namelijk het navigeren naar de definitie ervan en het inspecteren van de code.

### <a name="create-a-custom-vision-service-project"></a>Een Custom Vision Service-project maken

Met het eerste deel van de code wordt een afbeeldingsclassificatieproject gemaakt. Het project wordt weergegeven op de [Custom Vision-website](https://customvision.ai/), die u eerder hebt bezocht. Raadpleeg de [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) -methode voor het opgeven van andere opties wanneer u uw project maakt (Zie de hand leiding [een classificatie](../../getting-started-build-a-classifier.md) -webportal maken) voor meer informatie.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create)]

### <a name="create-tags-in-the-project"></a>Labels maken in het project

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Afbeeldingen uploaden en coderen

De voorbeeldafbeeldingen zijn opgenomen in de map **src/main/resources** van het project. Ze worden daar gelezen en met de bijbehorende labels naar de service geüpload.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload)]

Het vorige code fragment maakt gebruik van twee hulp functies waarmee de afbeeldingen als resource-streams worden opgehaald en geüpload naar de service (u kunt Maxi maal 64 installatie kopieën in één batch uploaden).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

### <a name="train-the-classifier-and-publish"></a>De classificatie trainen en publiceren

Deze code maakt de eerste iteratie van het Voorspellings model en publiceert die iteratie vervolgens naar het Voorspellings eindpunt. De naam die is opgegeven voor de gepubliceerde herhaling kan worden gebruikt voor het verzenden van voorspellings aanvragen. Er is geen iteratie beschikbaar in het Voorspellings eindpunt totdat het is gepubliceerd.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train)]

### <a name="use-the-prediction-endpoint"></a>Het Voorspellings eindpunt gebruiken

Het voorspellingseindpunt, dat door het `predictor`-object hier wordt weergegeven, is de verwijzing waarmee u een afbeelding naar het huidige model kunt verzenden en een classificatievoorspelling kunt verkrijgen. In dit voorbeeld wordt `predictor` elders gedefinieerd met de omgevingsvariabele voor de voorspellingssleutel.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_predict)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Als u de oplossing wilt compileren en uitvoeren met behulp van Maven, navigeert u naar de projectmap (**Vision/CustomVision**) in een opdracht prompt en voert u de volgende opdracht uit:

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

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt nu gezien hoe elke stap van de object detectie kan worden uitgevoerd in code. In dit voor beeld wordt één trainings iteratie uitgevoerd, maar vaak moet u het model meerdere keren trainen en testen om het nauw keuriger te maken.

> [!div class="nextstepaction"]
> [Een model testen en opnieuw trainen](../../test-your-model.md)
