---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 08/17/2020
ms.openlocfilehash: f54b5c7bec7d2b9af67b967ff34ab43bd1818a7d
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88511287"
---
Dit artikel biedt informatie en voorbeeldcode om u op weg te helpen met de Custom Vision-clientbibliotheek voor Python om een objectdetectiemodel te maken. Wanneer u het project hebt gemaakt, kunt u gelabelde regio's toevoegen, afbeeldingen uploaden, het project trainen, de gepubliceerde eindpunt-URL voor voorspellingen ophalen en het eindpunt gebruiken om afbeeldingen programmatisch te testen. Gebruik dit voorbeeld als een sjabloon om uw eigen Python-toepassing te maken.

## <a name="prerequisites"></a>Vereisten

- [Python 2.7+ of 3.5+](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/)-hulpprogramma
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="install-the-custom-vision-client-library"></a>De Custom Vision-clientbibliotheek installeren

Als u de Custom Vision Service-clientbibliotheek voor Python wilt installeren, voert u de volgende opdracht uit in PowerShell:

```powershell
pip install azure-cognitiveservices-vision-customvision
```

U kunt de afbeeldingen met de [Python-voorbeelden](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) downloaden.

[!INCLUDE [get-keys](../../includes/get-keys.md)]

[!INCLUDE [python-get-images](../../includes/python-get-images.md)]

## <a name="add-the-code"></a>Code toevoegen

Maak een nieuw bestand met de naam *sample.py* in uw projectmap.

### <a name="create-the-custom-vision-service-project"></a>Het Custom Vision Service-project maken

Als u een nieuw Custom Vision Service-project wilt maken, voegt u de volgende code aan uw script toe. Voeg uw abonnementssleutels in de juiste definities in. U kunt ook uw eindpunt-URL ophalen via de pagina Instellingen van de Custom Vision-website.

Raadpleeg de [create_project](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin?view=azure-python#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config-)-methode om andere opties op te geven wanneer u uw project maakt (uitgelegd in de webportalgids [Een detector maken](../../get-started-build-detector.md)).  

```Python
from azure.cognitiveservices.vision.customvision.training import CustomVisionTrainingClient
from azure.cognitiveservices.vision.customvision.training.models import ImageFileCreateBatch, ImageFileCreateEntry, Region
from msrest.authentication import ApiKeyCredentials

ENDPOINT = "<your API endpoint>"

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"
prediction_resource_id = "<your prediction resource id>"

publish_iteration_name = "detectModel"

credentials = ApiKeyCredentials(in_headers={"Training-key": training_key})
trainer = CustomVisionTrainingClient(ENDPOINT, credentials)

# Find the object detection domain
obj_detection_domain = next(domain for domain in trainer.get_domains() if domain.type == "ObjectDetection" and domain.name == "General")

# Create a new project
print ("Creating project...")
project = trainer.create_project("My Detection Project", domain_id=obj_detection_domain.id)
```

### <a name="create-tags-in-the-project"></a>Labels maken in het project

Voeg de volgende code toe aan het eind van *sample.py* om objecttags in uw project te maken:

```Python
# Make two tags in the new project
fork_tag = trainer.create_tag(project.id, "fork")
scissors_tag = trainer.create_tag(project.id, "scissors")
```

### <a name="upload-and-tag-images"></a>Afbeeldingen uploaden en labelen

Als u afbeeldingen labelt in objectdetectieprojecten, dient u de regio van elk gelabeld object op te geven met behulp van genormaliseerde coördinaten.

> [!NOTE]
> Als u geen hulpprogramma voor klikken en slepen hebt om de coördinaten van regio's te markeren, kunt u de Web-UI op [Customvision.ai](https://www.customvision.ai/) gebruiken. In dit voorbeeld zijn de coördinaten al opgenomen.


Als u de afbeeldingen, labels en regio's aan het project wilt toevoegen, voegt u de volgende code in nadat u het label hebt gemaakt. In deze zelfstudie zijn de regio's inline vastgelegd in de code zelf. Door de regio's wordt het begrenzingsvak opgegeven in genormaliseerde coördinaten. De coördinaten worden gegeven in de volgorde links, boven, breedte, hoogte.

```Python
fork_image_regions = {
    "fork_1": [ 0.145833328, 0.3509314, 0.5894608, 0.238562092 ],
    "fork_2": [ 0.294117659, 0.216944471, 0.534313738, 0.5980392 ],
    "fork_3": [ 0.09191177, 0.0682516545, 0.757352948, 0.6143791 ],
    "fork_4": [ 0.254901975, 0.185898721, 0.5232843, 0.594771266 ],
    "fork_5": [ 0.2365196, 0.128709182, 0.5845588, 0.71405226 ],
    "fork_6": [ 0.115196079, 0.133611143, 0.676470637, 0.6993464 ],
    "fork_7": [ 0.164215669, 0.31008172, 0.767156839, 0.410130739 ],
    "fork_8": [ 0.118872553, 0.318251669, 0.817401946, 0.225490168 ],
    "fork_9": [ 0.18259804, 0.2136765, 0.6335784, 0.643790841 ],
    "fork_10": [ 0.05269608, 0.282303959, 0.8088235, 0.452614367 ],
    "fork_11": [ 0.05759804, 0.0894935, 0.9007353, 0.3251634 ],
    "fork_12": [ 0.3345588, 0.07315363, 0.375, 0.9150327 ],
    "fork_13": [ 0.269607842, 0.194068655, 0.4093137, 0.6732026 ],
    "fork_14": [ 0.143382356, 0.218578458, 0.7977941, 0.295751631 ],
    "fork_15": [ 0.19240196, 0.0633497, 0.5710784, 0.8398692 ],
    "fork_16": [ 0.140931368, 0.480016381, 0.6838235, 0.240196079 ],
    "fork_17": [ 0.305147052, 0.2512582, 0.4791667, 0.5408496 ],
    "fork_18": [ 0.234068632, 0.445702642, 0.6127451, 0.344771236 ],
    "fork_19": [ 0.219362751, 0.141781077, 0.5919118, 0.6683006 ],
    "fork_20": [ 0.180147052, 0.239820287, 0.6887255, 0.235294119 ]
}

scissors_image_regions = {
    "scissors_1": [ 0.4007353, 0.194068655, 0.259803921, 0.6617647 ],
    "scissors_2": [ 0.426470578, 0.185898721, 0.172794119, 0.5539216 ],
    "scissors_3": [ 0.289215684, 0.259428144, 0.403186262, 0.421568632 ],
    "scissors_4": [ 0.343137264, 0.105833367, 0.332107842, 0.8055556 ],
    "scissors_5": [ 0.3125, 0.09766343, 0.435049027, 0.71405226 ],
    "scissors_6": [ 0.379901975, 0.24308826, 0.32107842, 0.5718954 ],
    "scissors_7": [ 0.341911763, 0.20714055, 0.3137255, 0.6356209 ],
    "scissors_8": [ 0.231617644, 0.08459154, 0.504901946, 0.8480392 ],
    "scissors_9": [ 0.170343131, 0.332957536, 0.767156839, 0.403594762 ],
    "scissors_10": [ 0.204656869, 0.120539248, 0.5245098, 0.743464053 ],
    "scissors_11": [ 0.05514706, 0.159754932, 0.799019635, 0.730392158 ],
    "scissors_12": [ 0.265931368, 0.169558853, 0.5061275, 0.606209159 ],
    "scissors_13": [ 0.241421565, 0.184264734, 0.448529422, 0.6830065 ],
    "scissors_14": [ 0.05759804, 0.05027781, 0.75, 0.882352948 ],
    "scissors_15": [ 0.191176474, 0.169558853, 0.6936275, 0.6748366 ],
    "scissors_16": [ 0.1004902, 0.279036, 0.6911765, 0.477124184 ],
    "scissors_17": [ 0.2720588, 0.131977156, 0.4987745, 0.6911765 ],
    "scissors_18": [ 0.180147052, 0.112369314, 0.6262255, 0.6666667 ],
    "scissors_19": [ 0.333333343, 0.0274019931, 0.443627447, 0.852941155 ],
    "scissors_20": [ 0.158088237, 0.04047389, 0.6691176, 0.843137264 ]
}
```

Gebruik vervolgens deze kaart met koppelingen om elke voorbeeldafbeelding met de bijbehorende regiocoördinaten te uploaden (u kunt maximaal 64 afbeeldingen in één batch uploaden). Voeg de volgende code toe.

> [!NOTE]
> U moet het pad naar de afbeeldingen wijzigen, gebaseerd op waar u de opslagplaats Cognitive Services Python-SDK-voorbeelden eerder hebt gedownload.

```Python
# Update this with the path to where you downloaded the images.
base_image_url = "<path to repo directory>/cognitive-services-python-sdk-samples/samples/vision/"

# Go through the data table above and create the images
print ("Adding images...")
tagged_images_with_regions = []

for file_name in fork_image_regions.keys():
    x,y,w,h = fork_image_regions[file_name]
    regions = [ Region(tag_id=fork_tag.id, left=x,top=y,width=w,height=h) ]

    with open(base_image_url + "images/fork/" + file_name + ".jpg", mode="rb") as image_contents:
        tagged_images_with_regions.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), regions=regions))

for file_name in scissors_image_regions.keys():
    x,y,w,h = scissors_image_regions[file_name]
    regions = [ Region(tag_id=scissors_tag.id, left=x,top=y,width=w,height=h) ]

    with open(base_image_url + "images/scissors/" + file_name + ".jpg", mode="rb") as image_contents:
        tagged_images_with_regions.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), regions=regions))

upload_result = trainer.create_images_from_files(project.id, ImageFileCreateBatch(images=tagged_images_with_regions))
if not upload_result.is_batch_successful:
    print("Image batch upload failed.")
    for image in upload_result.images:
        print("Image status: ", image.status)
    exit(-1)
```

### <a name="train-the-project-and-publish"></a>Het project trainen en publiceren

Met deze code wordt de eerste iteratie van het voorspellingsmodel gemaakt en vervolgens wordt die iteratie gepubliceerd naar het voorspellingseindpunt. De naam die is opgegeven voor de gepubliceerde iteratie, kan worden gebruikt voor het verzenden van voorspellingsaanvragen. Er is pas na publicatie een iteratie beschikbaar in het voorspellingseindpunt.

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

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>De gepubliceerde iteratie voor het voorspellingseindpunt ophalen en gebruiken

Als u een afbeelding naar het voorspellingseindpunt wilt verzenden en de voorspelling wilt ophalen, voegt u de volgende code toe aan het einde van het bestand:

```Python
from azure.cognitiveservices.vision.customvision.prediction import CustomVisionPredictionClient
from msrest.authentication import ApiKeyCredentials

# Now there is a trained endpoint that can be used to make a prediction
prediction_credentials = ApiKeyCredentials(in_headers={"Prediction-key": prediction_key})
predictor = CustomVisionPredictionClient(ENDPOINT, prediction_credentials)

# Open the sample image and get back the prediction results.
with open(base_image_url + "images/Test/test_od_image.jpg", mode="rb") as test_data:
    results = predictor.detect_image(project.id, publish_iteration_name, test_data)

# Display the results.    
for prediction in results.predictions:
    print("\t" + prediction.tag_name + ": {0:.2f}% bbox.left = {1:.2f}, bbox.top = {2:.2f}, bbox.width = {3:.2f}, bbox.height = {4:.2f}".format(prediction.probability * 100, prediction.bounding_box.left, prediction.bounding_box.top, prediction.bounding_box.width, prediction.bounding_box.height))
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer *sample.py* uit.

```powershell
python sample.py
```

De uitvoer van de toepassing moet in de console worden weergegeven. Vervolgens kunt u controleren of de testafbeelding (gevonden in **samples/vision/images/Test**) op de juiste wijze wordt gelabeld en of de detectieregio juist is.

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt nu gezien hoe elke stap van het objectdetectieproces in code kan worden uitgevoerd. Met dit voorbeeld wordt één trainingsiteratie uitgevoerd, maar vaak zult u uw model meerdere keren willen trainen en testen om het nauwkeuriger te maken. In de volgende trainingshandleiding wordt classificatie van afbeeldingen behandeld. De principes zijn soortgelijk aan die van objectdetectie.

> [!div class="nextstepaction"]
> [Een model testen en opnieuw trainen](../../test-your-model.md)
