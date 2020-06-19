---
title: 'Snelstartgids: Computer Vision-client bibliotheek voor python'
description: Ga met deze Snelstartgids aan de slag met de Computer Vision-client bibliotheek voor python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 32a6d7bf481b903cde4a777a10059d8ef2968ccd
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/18/2020
ms.locfileid: "85073342"
---
<a name="HOLTop"></a>

[Referentie documentatie](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)  |  [Bron code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-computervision)  |  van bibliotheek [Pakket (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-computervision/)  |  Voor [beelden](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3. x](https://www.python.org/)
* Wanneer u uw Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" maakt u een computer vision resource Maak "  target="_blank"> een computer vision resource <span class="docon docon-navigate-external x-hidden-focus"></span> </a> in de Azure Portal om uw sleutel en eind punt op te halen. Nadat de app is geïmplementeerd, klikt **u op Ga naar resource**.
    * U hebt de sleutel en het eind punt nodig van de resource die u maakt om de toepassing te verbinden met de Computer Vision-service. U plakt uw sleutel en het eind punt in de onderstaande code verderop in de Quick Start.
    * U kunt de gratis prijs categorie ( `F0` ) gebruiken om de service te proberen en later te upgraden naar een betaalde laag voor productie.
* [Maak omgevings variabelen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de sleutel-en eind punt-URL, `COMPUTER_VISION_SUBSCRIPTION_KEY` respectievelijk met de naam en `COMPUTER_VISION_ENDPOINT` .

## <a name="setting-up"></a>Instellen
 
### <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

Maak bijvoorbeeld een nieuwe python &mdash; -script*QuickStart-file.py*. Open het vervolgens in uw voorkeurs editor of IDE en importeer de volgende bibliotheken.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_imports)]

Maak vervolgens variabelen voor het Azure-eind punt en de sleutel van uw resource.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_vars)]

> [!NOTE]
> Als u de omgevings variabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell waarmee deze wordt uitgevoerd, sluiten en opnieuw openen om toegang te krijgen tot de variabele.

### <a name="install-the-client-library"></a>De client bibliotheek installeren

U kunt de client bibliotheek installeren met:

```console
pip install --upgrade azure-cognitiveservices-vision-computervision
```

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Computer Vision python SDK.

|Naam|Beschrijving|
|---|---|
|[ComputerVisionClientOperationsMixin](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin?view=azure-python)| Met deze klasse worden alle afbeeldings bewerkingen, zoals het analyseren van afbeeldingen, tekst detectie en het genereren van miniaturen, direct afgehandeld.|
| [ComputerVisionClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) | Deze klasse is nodig voor alle Computer Vision functionaliteit. U maakt de app met uw abonnements gegevens en gebruikt deze om instanties van andere klassen te maken. Het implementeert **ComputerVisionClientOperationsMixin**.|
|[VisualFeatureTypes](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python)| Deze opsomming definieert de verschillende typen afbeeldings analyse die kunnen worden uitgevoerd in een standaard analyse bewerking. U geeft een set **VisualFeatureTypes** -waarden op, afhankelijk van uw behoeften. |

## <a name="code-examples"></a>Codevoorbeelden

Deze code fragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Computer Vision-client bibliotheek voor python:

* [De client verifiëren](#authenticate-the-client)
* [Een afbeelding analyseren](#analyze-an-image)
* [Gedrukte en handgeschreven tekst lezen](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> In deze Quick Start wordt ervan uitgegaan dat u [een omgevings variabele hebt gemaakt](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) voor uw computer vision sleutel met de naam `COMPUTER_VISION_SUBSCRIPTION_KEY` .

Exemplaar een client met uw eind punt en sleutel. Maak een [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) -object met uw sleutel en gebruik het met uw eind punt om een [ComputerVisionClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) -object te maken.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_client)]

## <a name="analyze-an-image"></a>Een afbeelding analyseren

Sla een verwijzing op naar de URL van een afbeelding die u wilt analyseren.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_remoteimage)]

### <a name="get-image-description"></a>Beschrijving van afbeelding ophalen

Met de volgende code wordt de lijst met gegenereerde bijschriften voor de afbeelding opgehaald. Zie [afbeeldingen beschrijven](../../concept-describing-images.md) voor meer informatie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_describe)]

### <a name="get-image-category"></a>Categorie van installatie kopie ophalen

Met de volgende code wordt de gedetecteerde categorie van de afbeelding opgehaald. Zie [installatie kopieën categoriseren](../../concept-categorizing-images.md) voor meer informatie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_categorize)]

### <a name="get-image-tags"></a>Afbeeldings Tags ophalen

Met de volgende code wordt de set gedetecteerde labels in de afbeelding opgehaald. Zie [inhouds Tags](../../concept-tagging-images.md) voor meer informatie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_tags)]

### <a name="detect-objects"></a>Objecten detecteren

Met de volgende code worden algemene objecten in de installatie kopie gedetecteerd en worden deze in de-console afgedrukt. Zie [object detectie](../../concept-object-detection.md) voor meer informatie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_objects)]

### <a name="detect-brands"></a>Merken detecteren

Met de volgende code worden de bedrijfs merken en-logo's in de installatie kopie gedetecteerd en afgedrukt in de-console. Zie [merk detectie](../../concept-brand-detection.md) voor meer informatie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_brands)]

### <a name="detect-faces"></a>Gezichten detecteren

Met de volgende code worden de gedetecteerde gezichten in de afbeelding met hun rechthoek coördinaten als resultaat gegeven en selecteert u face Attributes. Zie [gezichts detectie](../../concept-detecting-faces.md) voor meer informatie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Inhoud voor volwassenen, ongepaste of benchmarks detecteren

Met de volgende code wordt de gedetecteerde aanwezigheid van inhoud voor volwassenen afgedrukt in de installatie kopie. Zie [inhoud voor volwassenen, ongepaste, benchmarks](../../concept-detecting-adult-content.md) voor meer informatie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Kleuren schema afbeelding ophalen

Met de volgende code worden de gedetecteerde kleur kenmerken in de afbeelding afgedrukt, zoals de dominante kleuren en accent kleur. Zie [kleuren schema's](../../concept-detecting-color-schemes.md) voor meer informatie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Domein-specifieke inhoud ophalen

Computer Vision kunt een speciaal model gebruiken om verdere analyse van installatie kopieën uit te voeren. Zie [Domain-specifieke inhoud](../../concept-detecting-domain-content.md) voor meer informatie. 

Met de volgende code worden gegevens over gedetecteerde beroemdheden in de installatie kopie geparseerd.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_celebs)]

Met de volgende code worden gegevens over gedetecteerde bezienswaardigheden in de installatie kopie geparseerd.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Het afbeeldings type ophalen

Met de volgende code wordt informatie over het type afbeelding afgedrukt &mdash; , ongeacht of het een illustratie of lijn tekening is.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Gedrukte en handgeschreven tekst lezen

Computer Vision kunt zicht bare tekst in een afbeelding lezen en deze converteren naar een teken stroom. Dit doet u in twee delen.

### <a name="call-the-read-api"></a>De Lees-API aanroepen

Gebruik eerst de volgende code om de **Lees** methode voor de gegeven afbeelding aan te roepen. Hiermee wordt een bewerkings-ID geretourneerd en wordt een asynchroon proces gestart om de inhoud van de afbeelding te lezen.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_call)]

### <a name="get-read-results"></a>Lees resultaten ophalen

Vervolgens haalt u de bewerkings-ID op die wordt geretourneerd door de **Lees** aanroep en gebruikt u deze om de service te doorzoeken op de resultaten van de bewerking. Met de volgende code wordt de bewerking met een interval van één seconde gecontroleerd totdat de resultaten worden geretourneerd. Vervolgens worden de geëxtraheerde tekst gegevens afgedrukt naar de-console.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_response)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de `python` opdracht in uw Quick Start-bestand.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure-CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)


## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u de Computer Vision-bibliotheek voor python kunt gebruiken om basis taken uit te voeren. Bekijk vervolgens de referentie documentatie voor meer informatie over de-bibliotheek.


> [!div class="nextstepaction"]
>[Computer Vision-API verwijzing (python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)

* [Wat is Computer Vision?](../../Home.md)
* De bron code voor dit voor beeld is te vinden op [github](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py).
