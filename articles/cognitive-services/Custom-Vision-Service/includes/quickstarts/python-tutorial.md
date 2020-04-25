---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 04/14/2020
ms.openlocfilehash: a28f1a63a4096c536f4c1f7f6c50d538821f236d
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82134150"
---
In dit artikel leest u hoe u aan de slag kunt met de Custom Vision SDK met python een model voor een installatie kopie maken. Nadat de app is gemaakt, kunt u Tags toevoegen, afbeeldingen uploaden, het project trainen, de gepubliceerde voor spelling-eind punt-URL van het project ophalen en het eind punt gebruiken om een afbeelding programmatisch te testen. Gebruik dit voorbeeld als een sjabloon om uw eigen Python-toepassing te maken. Zie de [handleiding voor browsers](../../getting-started-build-a-classifier.md) als u het ontwikkelproces wilt doorlopen en een classificatiemodel wilt gebruiken _zonder_ code.

## <a name="prerequisites"></a>Vereisten

- [Python 2.7+ of 3.5+](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/)-hulpprogramma
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="install-the-custom-vision-sdk"></a>De Custom Vision-SDK installeren

Als u de Custom Vision Service-SDK voor Python wilt installeren, voert u de volgende opdracht uit in PowerShell:

```powershell
pip install azure-cognitiveservices-vision-customvision
```

[!INCLUDE [get-keys](../../includes/get-keys.md)]

[!INCLUDE [python-get-images](../../includes/python-get-images.md)]

## <a name="add-the-code"></a>Code toevoegen

Maak een nieuw bestand met de naam *sample.py* in uw projectmap.

### <a name="create-the-custom-vision-service-project"></a>Het Custom Vision Service-project maken

Als u een nieuw Custom Vision Service-project wilt maken, voegt u de volgende code aan uw script toe. Voeg uw abonnementssleutels in de juiste definities in. U kunt ook uw eind punt-URL ophalen via de pagina instellingen van de website van Custom Vision.

Raadpleeg de [create_project](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.custom_vision_training_client.customvisiontrainingclient?view=azure-python#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config- ) -methode om andere opties op te geven wanneer u uw project maakt (Zie de hand leiding voor het [bouwen van een classificatie](../../getting-started-build-a-classifier.md) -webportal).  

```Python
from azure.cognitiveservices.vision.customvision.training import CustomVisionTrainingClient
from azure.cognitiveservices.vision.customvision.training.models import ImageFileCreateEntry

ENDPOINT = "<your API endpoint>"

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"
prediction_resource_id = "<your prediction resource id>"

publish_iteration_name = "classifyModel"

trainer = CustomVisionTrainingClient(training_key, endpoint=ENDPOINT)

# Create a new project
print ("Creating project...")
project = trainer.create_project("My New Project")
```

### <a name="create-tags-in-the-project"></a>Labels maken in het project

Voeg de volgende code toe aan het eind van *sample.py* om classificatielabels voor uw project te maken:

```Python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

### <a name="upload-and-tag-images"></a>Afbeeldingen uploaden en coderen

Als u de voorbeeldafbeeldingen aan het project wilt toevoegen, voegt u de volgende code in nadat u de tag hebt gemaakt. Met deze code wordt elke afbeelding met de bijbehorende tag geüpload. U kunt Maxi maal 64 installatie kopieën uploaden in één batch.

> [!NOTE]
> U moet het pad naar de installatie kopieën wijzigen op basis van waar u de Cognitive Services python SDK samples opslag plaats eerder hebt gedownload.

```Python
base_image_url = "<path to repo directory>/cognitive-services-python-sdk-samples/samples/vision/"

print("Adding images...")

image_list = []

for image_num in range(1, 11):
    file_name = "hemlock_{}.jpg".format(image_num)
    with open(base_image_url + "images/Hemlock/" + file_name, "rb") as image_contents:
        image_list.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), tag_ids=[hemlock_tag.id]))

for image_num in range(1, 11):
    file_name = "japanese_cherry_{}.jpg".format(image_num)
    with open(base_image_url + "images/Japanese Cherry/" + file_name, "rb") as image_contents:
        image_list.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), tag_ids=[cherry_tag.id]))

upload_result = trainer.create_images_from_files(project.id, images=image_list)
if not upload_result.is_batch_successful:
    print("Image batch upload failed.")
    for image in upload_result.images:
        print("Image status: ", image.status)
    exit(-1)
```

### <a name="train-the-classifier-and-publish"></a>De classificatie trainen en publiceren

Deze code maakt de eerste iteratie van het Voorspellings model en publiceert die iteratie vervolgens naar het Voorspellings eindpunt. De naam die is opgegeven voor de gepubliceerde herhaling kan worden gebruikt voor het verzenden van voorspellings aanvragen. Er is geen iteratie beschikbaar in het Voorspellings eindpunt totdat het is gepubliceerd.

```Python
import time

print ("Training...")
iteration = trainer.train_project(project.id)
while (iteration.status != "Completed"):
    iteration = trainer.get_iteration(project.id, iteration.id)
    print ("Training status: " + iteration.status)
    time.sleep(1)

# The iteration is now trained. Publish it to the project endpoint
trainer.publish_iteration(project.id, iteration.id, publish_iteration_name, prediction_resource_id)
print ("Done!")
```

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>De gepubliceerde iteratie op het Voorspellings eindpunt ophalen en gebruiken

Als u een afbeelding naar het voorspellingseindpunt wilt verzenden en de voorspelling wilt ophalen, voegt u de volgende code toe aan het einde van het bestand:

```python
from azure.cognitiveservices.vision.customvision.prediction import CustomVisionPredictionClient

# Now there is a trained endpoint that can be used to make a prediction
predictor = CustomVisionPredictionClient(prediction_key, endpoint=ENDPOINT)

with open(base_image_url + "images/Test/test_image.jpg", "rb") as image_contents:
    results = predictor.classify_image(
        project.id, publish_iteration_name, image_contents.read())

    # Display the results.
    for prediction in results.predictions:
        print("\t" + prediction.tag_name +
              ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer *sample.py* uit.

```powershell
python sample.py
```

Als het goed is, is de uitvoer van de toepassing vergelijkbaar met de volgende tekst:

```console
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

U kunt vervolgens controleren of de test installatie kopie (gevonden in **<base_image_url>installatie kopieën/test/**) op de juiste wijze is gelabeld. U kunt altijd teruggaan naar de [Custom Vision-website](https://customvision.ai) en de huidige status bekijken van het nieuwe project dat u hebt gemaakt.

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt nu gezien hoe elke stap van de object detectie kan worden uitgevoerd in code. In dit voor beeld wordt één trainings iteratie uitgevoerd, maar vaak moet u het model meerdere keren trainen en testen om het nauw keuriger te maken.

> [!div class="nextstepaction"]
> [Een model testen en opnieuw trainen](../../test-your-model.md)
