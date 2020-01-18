---
title: 'Snelstartgids: een afbeeldings classificatie project maken met de Custom Vision SDK voor node. js'
titleSuffix: Azure Cognitive Services
description: Maak een project, voeg tags toe, upload afbeeldingen, train uw project en doe een voorspelling met behulp van de Node.js-SDK.
services: cognitive-services
author: areddish
manager: daauld
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: areddish
ms.openlocfilehash: 7490e1261262ff26eec48a691e22ec177954dcf3
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169455"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-nodejs-sdk"></a>Snelstartgids: een afbeeldings classificatie project maken met de Custom Vision node. js-SDK

In dit artikel leest u hoe u aan de slag kunt met de Custom Vision SDK met node. js voor het bouwen van een afbeeldings classificatie model. Nadat de app is gemaakt, kunt u Tags toevoegen, afbeeldingen uploaden, het project trainen, de gepubliceerde voor spelling-eind punt-URL van het project ophalen en het eind punt gebruiken om een afbeelding programmatisch te testen. Gebruik dit voorbeeld als een sjabloon om uw eigen Node.js-toepassing te maken. Zie de [handleiding voor browsers](getting-started-build-a-classifier.md) als u het ontwikkelproces wilt doorlopen en een classificatiemodel wilt gebruiken _zonder_ code.

## <a name="prerequisites"></a>Vereisten

- [Node.js 8](https://www.nodejs.org/en/download/) of hoger geïnstalleerd.
- [npm](https://www.npmjs.com/) geïnstalleerd.
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="install-the-custom-vision-sdk"></a>De Custom Vision-SDK installeren

Als u de Custom Vision Service-SDK voor Node.js wilt installeren, voert u de volgende opdracht uit in PowerShell:

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [node-get-images](includes/node-get-images.md)]

## <a name="add-the-code"></a>Code toevoegen

Maak een nieuw bestand met de naam *sample.js* in uw projectmap.

### <a name="create-the-custom-vision-service-project"></a>Het Custom Vision Service-project maken

Als u een nieuw Custom Vision Service-project wilt maken, voegt u de volgende code aan uw script toe. Voeg uw abonnements sleutels in de juiste definities in en stel de sampleDataRoot in op het pad naar de map met installatie kopieën. Zorg ervoor dat de eindpunt waarde overeenkomt met de trainings-en Voorspellings eindpunten die u hebt gemaakt op [Customvision.ai](https://www.customvision.ai/). Houd er rekening mee dat het verschil tussen het maken van een object detectie-en afbeeldings classificatie project het domein is dat is opgegeven in de **createProject** -aanroep.

```javascript
const util = require('util');
const fs = require('fs');
const TrainingApiClient = require("@azure/cognitiveservices-customvision-training");
const PredictionApiClient = require("@azure/cognitiveservices-customvision-prediction");

const setTimeoutPromise = util.promisify(setTimeout);

const trainingKey = "<your training key>";
const predictionKey = "<your prediction key>";
const predictionResourceId = "<your prediction resource id>";
const sampleDataRoot = "<path to image files>";

const endPoint = "https://<my-resource-name>.cognitiveservices.azure.com/"

const publishIterationName = "classifyModel";

const trainer = new TrainingApiClient(trainingKey, endPoint);

(async () => {
    console.log("Creating project...");
    const sampleProject = await trainer.createProject("Sample Project")
```

### <a name="create-tags-in-the-project"></a>Labels maken in het project

Voeg de volgende code toe aan het eind van *sample.js* om classificatielabels voor uw project te maken:

```javascript
const hemlockTag = await trainer.createTag(sampleProject.id, "Hemlock");
const cherryTag = await trainer.createTag(sampleProject.id, "Japanese Cherry");
```

### <a name="upload-and-tag-images"></a>Afbeeldingen uploaden en labelen

Als u de voorbeeldafbeeldingen aan het project wilt toevoegen, voegt u de volgende code in nadat u de tag hebt gemaakt. Met deze code wordt elke afbeelding met de bijbehorende tag geüpload. U kunt Maxi maal 64 installatie kopieën uploaden in één batch.

> [!NOTE]
> U moet *sampleDataRoot* wijzigen in het pad naar de afbeeldingen, gebaseerd op waar u het project Cognitive Services Node.js-SDK-voorbeelden eerder hebt gedownload.

```javascript
console.log("Adding images...");
let fileUploadPromises = [];

const hemlockDir = `${sampleDataRoot}/Hemlock`;
const hemlockFiles = fs.readdirSync(hemlockDir);
hemlockFiles.forEach(file => {
    fileUploadPromises.push(trainer.createImagesFromData(sampleProject.id, fs.readFileSync(`${hemlockDir}/${file}`), { tagIds: [hemlockTag.id] }));
});

const cherryDir = `${sampleDataRoot}/Japanese Cherry`;
const japaneseCherryFiles = fs.readdirSync(cherryDir);
japaneseCherryFiles.forEach(file => {
    fileUploadPromises.push(trainer.createImagesFromData(sampleProject.id, fs.readFileSync(`${cherryDir}/${file}`), { tagIds: [cherryTag.id] }));
});

await Promise.all(fileUploadPromises);
```

### <a name="train-the-classifier-and-publish"></a>De classificatie trainen en publiceren

Deze code maakt de eerste iteratie van het Voorspellings model en publiceert die iteratie vervolgens naar het Voorspellings eindpunt. De naam die is opgegeven voor de gepubliceerde herhaling kan worden gebruikt voor het verzenden van voorspellings aanvragen. Er is geen iteratie beschikbaar in het Voorspellings eindpunt totdat het is gepubliceerd.

```javascript
console.log("Training...");
let trainingIteration = await trainer.trainProject(sampleProject.id);

// Wait for training to complete
console.log("Training started...");
while (trainingIteration.status == "Training") {
    console.log("Training status: " + trainingIteration.status);
    await setTimeoutPromise(1000, null);
    trainingIteration = await trainer.getIteration(sampleProject.id, trainingIteration.id)
}
console.log("Training status: " + trainingIteration.status);

// Publish the iteration to the end point
await trainer.publishIteration(sampleProject.id, trainingIteration.id, publishIterationName, predictionResourceId);
```

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>De gepubliceerde iteratie op het Voorspellings eindpunt ophalen en gebruiken

Als u een afbeelding naar het voorspellingseindpunt wilt verzenden en de voorspelling wilt ophalen, voegt u de volgende code toe aan het einde van het bestand:

```javascript
    const predictor = new PredictionApiClient(predictionKey, endPoint);
    const testFile = fs.readFileSync(`${sampleDataRoot}/Test/test_image.jpg`);

    const results = await predictor.classifyImage(sampleProject.id, publishIterationName, testFile);

    // Step 6. Show results
    console.log("Results:");
    results.predictions.forEach(predictedResult => {
        console.log(`\t ${predictedResult.tagName}: ${(predictedResult.probability * 100.0).toFixed(2)}%`);
    });
})()
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer *sample.js* uit.

```shell
node sample.js
```

Als het goed is, is de uitvoer van de toepassing vergelijkbaar met de volgende tekst:

```console
Creating project...
Adding images...
Training...
Training started...
Training status: Training
Training status: Training
Training status: Training
Training status: Completed
Results:
         Hemlock: 94.97%
         Japanese Cherry: 0.01%
```

U kunt vervolgens controleren of de testafbeelding (in **<base_image_url>/Images/Test/** ) juist is getagd. U kunt altijd teruggaan naar de [Custom Vision-website](https://customvision.ai) en de huidige status bekijken van het nieuwe project dat u hebt gemaakt.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt nu gezien hoe elke stap van het afbeeldingsclassificatieproces in code kan worden uitgevoerd. Met dit voorbeeld wordt één trainingsinteratie uitgevoerd, maar vaak zult u uw model meerdere keren willen trainen en testen om het nauwkeuriger te maken.

> [!div class="nextstepaction"]
> [Een model testen en opnieuw trainen](test-your-model.md)
