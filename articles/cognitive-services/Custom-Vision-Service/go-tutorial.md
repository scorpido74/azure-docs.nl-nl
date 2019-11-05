---
title: 'Snelstartgids: een afbeeldings classificatie project maken met de Custom Vision SDK voor Go'
titleSuffix: Azure Cognitive Services
description: Maak een project, voeg tags toe, upload afbeeldingen, train uw project en doe een voorspelling met behulp van de Go-SDK.
services: cognitive-services
author: areddish
manager: daauld
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 08/08/2019
ms.author: areddish
ms.openlocfilehash: 7f78230028b66fb12e52f7d5e96cbecbfe8b99a3
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "73519469"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-go-sdk"></a>Snelstartgids: een afbeeldings classificatie project maken met de Custom Vision go-SDK

Dit artikel biedt informatie en voorbeeldcode om u op weg te helpen met de Custom Vision-SDK met Go een afbeeldingsclassificatiemodel te maken. Nadat de app is gemaakt, kunt u Tags toevoegen, afbeeldingen uploaden, het project trainen, de gepubliceerde voor spelling-eind punt-URL van het project ophalen en het eind punt gebruiken om een afbeelding programmatisch te testen. Gebruik dit voorbeeld als een sjabloon om uw eigen Go-toepassing te maken. Zie de _handleiding voor browsers_ als u het ontwikkelproces wilt doorlopen en een classificatiemodel wilt gebruiken [zonder](getting-started-build-a-classifier.md) code.

## <a name="prerequisites"></a>Vereisten

- [Go 1.8+](https://golang.org/doc/install)
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="install-the-custom-vision-sdk"></a>De Custom Vision-SDK installeren

Als u de Custom Vision Service-SDK voor Go wilt installeren, voert u de volgende opdracht uit in PowerShell:

```shell
go get -u github.com/Azure/azure-sdk-for-go/...
```

of als u `dep`gebruikt, voert u de volgende handelingen uit in uw opslag plaats:
```shell
dep ensure -add github.com/Azure/azure-sdk-for-go
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]

## <a name="add-the-code"></a>Code toevoegen

Maak een nieuw bestand met de naam *sample.go* in uw projectmap.

### <a name="create-the-custom-vision-service-project"></a>Het Custom Vision Service-project maken

Als u een nieuw Custom Vision Service-project wilt maken, voegt u de volgende code aan uw script toe. Voeg uw abonnementssleutels in de juiste definities in. U kunt ook uw eind punt-URL ophalen via de pagina instellingen van de website van Custom Vision.

Raadpleeg de [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) -methode om andere opties op te geven wanneer u uw project maakt (Zie de hand leiding [een classificatie](getting-started-build-a-classifier.md) -webportal bouwen).

```go
import(
    "context"
    "bytes"
    "fmt"
    "io/ioutil"
    "path"
    "log"
    "time"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v3.0/customvision/training"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v3.0/customvision/prediction"
)

var (
    training_key string = "<your training key>"
    prediction_key string = "<your prediction key>"
    prediction_resource_id = "<your prediction resource id>"
    endpoint string = "<your endpoint URL>"
    project_name string = "Go Sample Project"
    iteration_publish_name = "classifyModel"
    sampleDataDirectory = "<path to sample images>"
)

func main() {
    fmt.Println("Creating project...")

    ctx = context.Background()

    trainer := training.New(training_key, endpoint)

    project, err := trainer.CreateProject(ctx, project_name, "sample project", nil, string(training.Multilabel))
    if (err != nil) {
        log.Fatal(err)
    }
```

### <a name="create-tags-in-the-project"></a>Labels maken in het project

Voeg de volgende code toe aan het eind van *sample.go* om classificatielabels voor uw project te maken:

```go
// Make two tags in the new project
hemlockTag, _ := trainer.CreateTag(ctx, *project.ID, "Hemlock", "Hemlock tree tag", string(training.Regular))
cherryTag, _ := trainer.CreateTag(ctx, *project.ID, "Japanese Cherry", "Japanese cherry tree tag", string(training.Regular))
```

### <a name="upload-and-tag-images"></a>Afbeeldingen uploaden en labelen

Als u de voorbeeldafbeeldingen aan het project wilt toevoegen, voegt u de volgende code in nadat u de tag hebt gemaakt. Met deze code wordt elke afbeelding met de bijbehorende tag geüpload. U kunt Maxi maal 64 installatie kopieën uploaden in één batch.

> [!NOTE]
> U moet het pad naar de afbeeldingen wijzigen, gebaseerd op waar u het project Cognitive Services Go-SDK-voorbeelden eerder hebt gedownload.

```go
fmt.Println("Adding images...")
japaneseCherryImages, err := ioutil.ReadDir(path.Join(sampleDataDirectory, "Japanese Cherry"))
if err != nil {
    fmt.Println("Error finding Sample images")
}

hemLockImages, err := ioutil.ReadDir(path.Join(sampleDataDirectory, "Hemlock"))
if err != nil {
    fmt.Println("Error finding Sample images")
}

for _, file := range hemLockImages {
    imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Hemlock", file.Name()))
    imageData := ioutil.NopCloser(bytes.NewReader(imageFile))

    trainer.CreateImagesFromData(ctx, *project.ID, imageData, []string{ hemlockTag.ID.String() })
}

for _, file := range japaneseCherryImages {
    imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Japanese Cherry", file.Name()))
    imageData := ioutil.NopCloser(bytes.NewReader(imageFile))
    trainer.CreateImagesFromData(ctx, *project.ID, imageData, []string{ cherryTag.ID.String() })
}
```

### <a name="train-the-classifier-and-publish"></a>De classificatie trainen en publiceren

Deze code maakt de eerste iteratie in het project en publiceert die iteratie vervolgens naar het Voorspellings eindpunt. De naam die is opgegeven voor de gepubliceerde herhaling kan worden gebruikt voor het verzenden van voorspellings aanvragen. Er is geen iteratie beschikbaar in het Voorspellings eindpunt totdat het is gepubliceerd.

```go
fmt.Println("Training...")
iteration, _ := trainer.TrainProject(ctx, *project.ID)
for {
    if *iteration.Status != "Training" {
        break
    }
    fmt.Println("Training status: " + *iteration.Status)
    time.Sleep(1 * time.Second)
    iteration, _ = trainer.GetIteration(ctx, *project.ID, *iteration.ID)
}
fmt.Println("Training status: " + *iteration.Status)

trainer.PublishIteration(ctx, *project.ID, *iteration.ID, iteration_publish_name, prediction_resource_id))
```

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>De gepubliceerde iteratie op het Voorspellings eindpunt ophalen en gebruiken

Als u een afbeelding naar het voorspellingseindpunt wilt verzenden en de voorspelling wilt ophalen, voegt u de volgende code toe aan het einde van het bestand:

```go
    fmt.Println("Predicting...")
    predictor := prediction.New(prediction_key, endpoint)

    testImageData, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Test", "test_image.jpg"))
    results, _ := predictor.ClassifyImage(ctx, *project.ID, iteration_publish_name, ioutil.NopCloser(bytes.NewReader(testImageData)), "")

    for _, prediction := range *results.Predictions {
        fmt.Printf("\t%s: %.2f%%", *prediction.TagName, *prediction.Probability * 100)
        fmt.Println("")
    }
}
```

## <a name="run-the-application"></a>De toepassing uitvoeren

*sample.go* uitvoeren.

```shell
go run sample.go
```

Als het goed is, is de uitvoer van de toepassing vergelijkbaar met de volgende tekst:

```console
Creating project...
Adding images...
Training...
Training status: Training
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

U kunt vervolgens controleren of de testafbeelding (in **<base_image_url>/Images/Test/** ) juist is getagd. U kunt altijd teruggaan naar de [Custom Vision-website](https://customvision.ai) en de huidige status bekijken van het nieuwe project dat u hebt gemaakt.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt nu gezien hoe elke stap van het afbeeldingsclassificatieproces in code kan worden uitgevoerd. Met dit voorbeeld wordt één trainingsinteratie uitgevoerd, maar vaak zult u uw model meerdere keren willen trainen en testen om het nauwkeuriger te maken.

> [!div class="nextstepaction"]
> [Een model testen en opnieuw trainen](test-your-model.md)