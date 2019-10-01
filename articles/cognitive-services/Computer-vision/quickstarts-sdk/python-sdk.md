---
title: 'Quickstart: Computer Vision-client bibliotheek voor python | Microsoft Docs'
description: Ga aan de slag met de Computer Vision-client bibliotheek voor python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 09/10/2019
ms.author: pafarley
ms.openlocfilehash: c0a11c90b59c1c475f80b82073c6b151d57535c3
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709791"
---
# <a name="quickstart-computer-vision-client-library-for-python"></a>Quickstart: Client bibliotheek voor python Computer Vision

De Computer Vision-service geeft ontwikkelaars toegang tot geavanceerde algoritmen voor het verwerken van afbeeldingen en het retourneren van informatie. Met Computer Vision-algoritmen kunt u de inhoud van een afbeelding op verschillende manieren analyseren, afhankelijk van de visuele kenmerken waarin u geïnteresseerd bent.

Gebruik de Computer Vision-client bibliotheek voor python voor het volgende:

* Analyseer een afbeelding voor Tags, tekst beschrijving, gezichten, inhoud voor volwassenen en meer.
* Gedrukte en handgeschreven tekst herkennen met de batch-API voor lezen.

> [!NOTE]
> De scenario's in deze Snelstartgids gebruiken Url's voor externe installatie kopieën. Zie de code op [github](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py)voor voorbeeld code die dezelfde bewerkingen op lokale installatie kopieën uitvoert.

[](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision) | PiPy-voor[beelden](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0) ([Source code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-computervision) | [package)](https://pypi.org/project/azure-cognitiveservices-vision-computervision/) | voor referentie documentatie

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Instellen

### <a name="create-a-computer-vision-azure-resource"></a>Een Computer Vision Azure-resource maken

Azure-Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een resource voor Computer Vision met behulp van de [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure cli](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine. U kunt ook het volgende doen:

* Ontvang een [proef sleutel](https://azure.microsoft.com/try/cognitive-services/#decision) die zeven dagen gratis geldig is. Nadat u zich hebt aangemeld, is deze beschikbaar op de [Azure-website](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Bekijk uw resource op het [Azure Portal](https://portal.azure.com/)

Nadat u een sleutel van uw proef abonnement of resource hebt ontvangen, [maakt u omgevings variabelen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de sleutel en het `COMPUTER_VISION_SUBSCRIPTION_KEY` eind punt-URL, respectievelijk met de naam en. `COMPUTER_VISION_ENDPOINT`
 
### <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

Maak bijvoorbeeld een nieuwe python&mdash;-script*QuickStart-file.py*. Open het vervolgens in uw voorkeurs editor of IDE en importeer de volgende bibliotheken.

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

## <a name="object-model"></a>Object model

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Computer Vision python SDK.

|Name|Description|
|---|---|
|[ComputerVisionClientOperationsMixin](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin?view=azure-python)| Met deze klasse worden alle afbeeldings bewerkingen, zoals het analyseren van afbeeldingen, tekst detectie en het genereren van miniaturen, direct afgehandeld.|
| [ComputerVisionClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) | Deze klasse is nodig voor alle Computer Vision functionaliteit. U maakt de app met uw abonnements gegevens en gebruikt deze om instanties van andere klassen te maken. Het implementeert **ComputerVisionClientOperationsMixin**.|
|[VisualFeatureTypes](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python)| Deze opsomming definieert de verschillende typen afbeeldings analyse die kunnen worden uitgevoerd in een standaard analyse bewerking. U geeft een set **VisualFeatureTypes** -waarden op, afhankelijk van uw behoeften. |

## <a name="code-examples"></a>Code voorbeelden

Deze code fragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Computer Vision-client bibliotheek voor python:

* [De client verifiëren](#authenticate-the-client)
* [Een afbeelding analyseren](#analyze-an-image)
* [Gedrukte en handgeschreven tekst lezen](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> In deze Quick Start wordt ervan uitgegaan dat u [een omgevings variabele](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) hebt gemaakt `COMPUTER_VISION_SUBSCRIPTION_KEY`voor uw computer vision sleutel met de naam.

Exemplaar een client met uw eind punt en sleutel. Maak een [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) -object met uw sleutel en gebruik het met uw eind punt om een [ComputerVisionClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) -object te maken.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_client)]

## <a name="analyze-an-image"></a>Een afbeelding analyseren

Sla een verwijzing op naar de URL van een afbeelding die u wilt analyseren.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_remoteimage)]

### <a name="get-image-description"></a>Beschrijving van afbeelding ophalen

Met de volgende code wordt de lijst met gegenereerde bijschriften voor de afbeelding opgehaald. Zie [afbeeldingen beschrijven](../concept-describing-images.md) voor meer informatie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_describe)]

### <a name="get-image-category"></a>Categorie van installatie kopie ophalen

Met de volgende code wordt de gedetecteerde categorie van de afbeelding opgehaald. Zie [installatie kopieën categoriseren](../concept-categorizing-images.md) voor meer informatie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_categorize)]

### <a name="get-image-tags"></a>Afbeeldings Tags ophalen

Met de volgende code wordt de set gedetecteerde labels in de afbeelding opgehaald. Zie [inhouds Tags](../concept-tagging-images.md) voor meer informatie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_tags)]

### <a name="detect-objects"></a>Objecten detecteren

Met de volgende code worden algemene objecten in de installatie kopie gedetecteerd en worden deze in de-console afgedrukt. Zie [object detectie](../concept-object-detection.md) voor meer informatie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_objects)]

### <a name="detect-brands"></a>Brands detecteren

Met de volgende code worden de bedrijfs merken en-logo's in de installatie kopie gedetecteerd en afgedrukt in de-console. Zie [merk detectie](../concept-brand-detection.md) voor meer informatie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_brands)]

### <a name="detect-faces"></a>Gezichten detecteren

Met de volgende code worden de gedetecteerde gezichten in de afbeelding met hun rechthoek coördinaten als resultaat gegeven en selecteert u face Attributes. Zie [gezichts detectie](../concept-detecting-faces.md) voor meer informatie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_faces)]

### <a name="detect-adult-or-racy-content"></a>Inhoud voor volwassenen of ongepaste detecteren

Met de volgende code wordt de gedetecteerde aanwezigheid van inhoud voor volwassenen of ongepaste in de installatie kopie afgedrukt. Zie [inhoud voor volwassenen en ongepaste](../concept-detecting-adult-content.md) voor meer informatie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Kleuren schema afbeelding ophalen

Met de volgende code worden de gedetecteerde kleur kenmerken in de afbeelding afgedrukt, zoals de dominante kleuren en accent kleur. Zie [kleuren schema's](../concept-detecting-color-schemes.md) voor meer informatie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Domein-specifieke inhoud ophalen

Computer Vision kunt een speciaal model gebruiken om verdere analyse van installatie kopieën uit te voeren. Zie [Domain-specifieke inhoud](../concept-detecting-domain-content.md) voor meer informatie. 

Met de volgende code worden gegevens over gedetecteerde beroemdheden in de installatie kopie geparseerd.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_celebs)]

Met de volgende code worden gegevens over gedetecteerde bezienswaardigheden in de installatie kopie geparseerd.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Het afbeeldings type ophalen

Met de volgende code wordt informatie over het type afbeelding&mdash;afgedrukt, ongeacht of het een illustratie of lijn tekening is.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Gedrukte en handgeschreven tekst lezen

Computer Vision kunt zicht bare tekst in een afbeelding lezen en deze converteren naar een teken stroom. Dit doet u in twee delen.

### <a name="call-the-read-api"></a>De Lees-API aanroepen

Gebruik eerst de volgende code om de **batch_read_file** -methode voor de gegeven afbeelding aan te roepen. Hiermee wordt een bewerkings-ID geretourneerd en wordt een asynchroon proces gestart om de inhoud van de afbeelding te lezen.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_call)]

### <a name="get-read-results"></a>Lees resultaten ophalen

Haal vervolgens de bewerkings-ID op die wordt geretourneerd door de **batch_read_file** -aanroep en gebruik deze om de service te doorzoeken op de resultaten van de bewerking. Met de volgende code wordt de bewerking met een interval van één seconde gecontroleerd totdat de resultaten worden geretourneerd. Vervolgens worden de geëxtraheerde tekst gegevens afgedrukt naar de-console.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_response)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met `python` de opdracht in uw Quick Start-bestand.

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

* [Wat is de Computer Vision-API?](../Home.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py).
