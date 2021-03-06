---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/25/2020
ms.openlocfilehash: 69f492ad73e60f8478772dc8b5b22bc19a5fb0e4
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2020
ms.locfileid: "94341034"
---
Aan de slag met de Custom Vision-clientbibliotheek voor Python. Volg deze stappen om het pakket te installeren en de voorbeeldcode voor het bouwen van een model voor de classificatie van afbeeldingen uit te proberen. U maakt een project, voegt tags toe, traint het project en gebruikt de voorspellingseindpunt-URL van het project om het programmatisch te testen. Gebruik dit voorbeeld als een sjabloon om uw eigen beeldherkennings-app te maken.

> [!NOTE]
> Als u een classificatiemodel wilt bouwen en trainen _zonder_ code te schrijven, raadpleegt u de [handleiding voor browsers](../../getting-started-build-a-classifier.md).

Gebruik de Custom Vision-clientbibliotheek voor Python voor het volgende:

* Een nieuw project maken in de Custom Vision-service
* Label aan het project toevoegen
* Afbeeldingen uploaden en labelen
* Het project trainen
* De huidige herhaling publiceren
* Voorspellingseindpunt testen

[Referentiedocumentatie](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/customvision?view=azure-python) | [Broncode bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-customvision/azure/cognitiveservices/vision/customvision) | [Package (PyPI)](https://pypi.org/project/azure-cognitiveservices-vision-customvision/) | [Voorbeelden](https://docs.microsoft.com/samples/browse/?products=azure&term=vision&terms=vision&languages=python)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
* Zodra u uw Azure-abonnement hebt, <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title="een Custom Vision-resource maken"  target="_blank"> maakt u een Custom Vision-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in de Azure-portal om een trainings- en voorspellingsresource te maken en uw sleutels en eindpunt op te halen. Wacht tot deze is geïmplementeerd en klik op de knop **Naar de resource gaan**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met Custom Vision. Later in de quickstart plakt u uw sleutels en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Als u een beeldanalyse-app wilt schrijven met Custom Vision voor Python, hebt u de Custom Vision-clientbibliotheek nodig. Nadat u Python hebt geïnstalleerd, voert u de volgende opdracht uit in PowerShell of een consolevenster:

```powershell
pip install azure-cognitiveservices-vision-customvision
```

### <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

Maak een nieuw Python-bestand en importeer de volgende bibliotheken.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_imports)]

> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Die is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ImageClassification/CustomVisionQuickstart.py), waar de codevoorbeelden uit deze quickstart zich bevinden.

Maak variabelen voor het Azure-eindpunt en de abonnementssleutels van uw resource.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_creds)]

> [!IMPORTANT]
> Ga naar Azure Portal. Als de Custom Vision-resources die u in de sectie **Vereisten** hebt gemaakt, zijn geïmplementeerd, klikt u onder **Volgende stappen** op de knop **Ga naar resource**. U vindt de sleutels en het eindpunt op de pagina's over **sleutel en eindpunt** van de resources, onder **Resourcebeheer**. U moet uw trainings- en voorspellingssleutels ophalen.
>
> U kunt de resource-id van de voorspelling vinden op het tabblad **Overzicht** , vermeld als **Abonnements-id**.
>
> Vergeet niet de sleutels uit uw code te verwijderen wanneer u klaar bent, en maak deze sleutels nooit openbaar. Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. Zie het artikel Cognitive Services [Beveiliging](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) voor meer informatie.

## <a name="object-model"></a>Objectmodel

|Naam|Beschrijving|
|---|---|
|[CustomVisionTrainingClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient?view=azure-python) | Deze klasse behandelt het maken, trainen en publiceren van uw modellen. |
|[CustomVisionPredictionClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-python)| Deze klasse verwerkt de query op uw modellen voor voorspellingen met betrekking tot de classificatie van afbeeldingen.|
|[ImagePrediction](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.models.imageprediction?view=azure-python)| Deze klasse definieert een voorspelling van één object op één afbeelding. Het bevat eigenschappen voor de object-id en -naam, de locatie van het begrenzingsvak van het object en een betrouwbaarheidsscore.|

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u het volgende kunt uitvoeren met de Custom Vision-clientbibliotheek voor Python:

* [De client verifiëren](#authenticate-the-client)
* [Een nieuw project maken in de Custom Vision-service](#create-a-new-custom-vision-project)
* [Label aan het project toevoegen](#add-tags-to-the-project)
* [Afbeeldingen uploaden en labelen](#upload-and-tag-images)
* [Het project trainen](#train-the-project)
* [De huidige herhaling publiceren](#publish-the-current-iteration)
* [Voorspellingseindpunt testen](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>De client verifiëren

Instantieer een exemplaar van een trainings- en voorspellingsclient met uw eindpunt en sleutels. Maak **ApiKeyServiceClientCredentials** -objecten met uw sleutels en gebruik deze met uw eindpunt om een [CustomVisionTrainingClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient?view=azure-python)- en [CustomVisionPredictionClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-python)-object te maken.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Een nieuw project maken in de Custom Vision-service

Als u een nieuw Custom Vision Service-project wilt maken, voegt u de volgende code aan uw script toe. 

Raadpleeg de [create_project](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin?view=azure-python#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config-)-methode om andere opties op te geven wanneer u uw project maakt (uitgelegd in de webportalgids [Een classificatie maken](../../getting-started-build-a-classifier.md)).  

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_create)]

## <a name="add-tags-to-the-project"></a>Label aan het project toevoegen

Voeg de volgende code toe om classificatietags toe te voegen aan uw project:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_tags)]


## <a name="upload-and-tag-images"></a>Afbeeldingen uploaden en labelen

Download eerst de voorbeeldafbeeldingen voor dit project. Sla de inhoud van de [map Voorbeeldafbeeldingen](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) op uw lokale apparaat op.

Als u de voorbeeldafbeeldingen aan het project wilt toevoegen, voegt u de volgende code in nadat u de tag hebt gemaakt. Met deze code wordt elke afbeelding met de bijbehorende tag geüpload. U kunt maximaal 64 afbeeldingen uploaden in één batch.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_tags)]

> [!NOTE]
> U moet het pad naar de afbeeldingen wijzigen, gebaseerd op waar u de opslagplaats Cognitive Services Python-SDK-voorbeelden hebt gedownload.

## <a name="train-the-project"></a>Het project trainen

Met deze code wordt de eerste iteratie van het voorspellingsmodel gemaakt. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_train)]

> [!TIP]
> Trainen met geselecteerde tags
>
> Indien gewenst kun u alleen trainen op een subset van de toegepaste tags. U kunt dit doen als u bepaalde tags nog niet vaak genoeg hebt toegepast, maar u wel voldoende andere codes hebt toegepast. Stel in de **[train_project](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin?view=azure-python#train-project-project-id--training-type-none--reserved-budget-in-hours-0--force-train-false--notification-email-address-none--selected-tags-none--custom-headers-none--raw-false----operation-config-&preserve-view=true)** -aanroep de optionele parameter *selected_tags* in op een lijst met de ID-tekenreeksen van de tags die u wilt gebruiken. Het model wordt getraind om alleen de tags in de lijst te herkennen.

## <a name="publish-the-current-iteration"></a>De huidige herhaling publiceren

Er is pas na publicatie een iteratie beschikbaar in het voorspellingseindpunt. Met de volgende code wordt de huidige iteratie van het model beschikbaar gemaakt voor het uitvoeren van query's. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Voorspellingseindpunt testen

Als u een afbeelding naar het voorspellingseindpunt wilt verzenden en de voorspelling wilt ophalen, voegt u de volgende code toe aan het einde van het bestand:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_test)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer *CustomVisionQuickstart.py* uit.

```powershell
python CustomVisionQuickstart.py
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

U kunt vervolgens controleren of de testafbeelding (in **<base_image_location>/images/Test/** ) juist is getagd. U kunt altijd teruggaan naar de [Custom Vision-website](https://customvision.ai) en de huidige status bekijken van het nieuwe project dat u hebt gemaakt.

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt nu gezien hoe elke stap van het afbeeldingsclassificatieproces in code kan worden uitgevoerd. Met dit voorbeeld wordt één trainingsiteratie uitgevoerd, maar vaak zult u uw model meerdere keren willen trainen en testen om het nauwkeuriger te maken.

> [!div class="nextstepaction"]
> [Een model testen en opnieuw trainen](../../test-your-model.md)

* Wat is Custom Vision?
* De broncode voor dit voorbeeld is te vinden [op GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ImageClassification/CustomVisionQuickstart.py)
* [SDK-naslagdocumentatie](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/customvision?view=azure-python)