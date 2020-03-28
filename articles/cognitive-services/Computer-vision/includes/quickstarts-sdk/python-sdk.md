---
title: 'Snelstart: Computer Vision-clientbibliotheek voor Python'
description: Ga aan de slag met de Computer Vision-clientbibliotheek voor Python met deze quickstart.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 5f552dfc320459d23e538e65e5f34c085f5b5ad9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80136005"
---
<a name="HOLTop"></a>

[Voorbeeld van broncode](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision) |  | van[naslagdocumentatieBibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-computervision)[(PiPy)-voorbeelden](https://pypi.org/project/azure-cognitiveservices-vision-computervision/) | [Samples](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Instellen

### <a name="create-a-computer-vision-azure-resource"></a>Een Azure-bron voor Computer Vision maken

Azure Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een bron voor Computer Vision met de [Azure-portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine. U kunt ook het volgende doen:

* Ontvang een [proefsleutel](https://azure.microsoft.com/try/cognitive-services/#decision) die zeven dagen gratis geldig is. Nadat u zich hebt aangemeld, is deze beschikbaar op de [Azure-website.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Uw bron weergeven op de [Azure-portal](https://portal.azure.com/)

Nadat u een sleutel uit uw proefabonnement of resource hebt opgehaald, maakt `COMPUTER_VISION_SUBSCRIPTION_KEY` u `COMPUTER_VISION_ENDPOINT` [omgevingsvariabelen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor respectievelijk de naam van de sleutel- en eindpunt-URL.
 
### <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

Maak bijvoorbeeld een&mdash;nieuw Python-script*quickstart-file.py.* Open deze vervolgens in uw voorkeurseditor of IDE en importeer de volgende bibliotheken.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_imports)]

Maak vervolgens variabelen voor het Azure-eindpunt en de sleutel van uw resource.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_vars)]

> [!NOTE]
> Als u de omgevingsvariabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell sluiten en opnieuw openen om toegang te krijgen tot de variabele.

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

U de clientbibliotheek installeren met:

```console
pip install --upgrade azure-cognitiveservices-vision-computervision
```

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces behandelen enkele van de belangrijkste functies van de Computer Vision Python SDK.

|Name|Beschrijving|
|---|---|
|[ComputerVisionClientOperationsMixin](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin?view=azure-python)| Deze klasse verwerkt rechtstreeks alle afbeeldingsbewerkingen, zoals beeldanalyse, tekstdetectie en miniatuurgeneratie.|
| [ComputerVisionClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) | Deze klasse is nodig voor alle Computer Vision-functionaliteit. U wilt deze met uw abonnementsgegevens en u gebruikt deze om exemplaren van andere klassen te produceren. Het implementeert **ComputerVisionClientOperationsMixin**.|
|[VisualFeatureTypes](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python)| Dit enum definieert de verschillende soorten beeldanalyse die kunnen worden uitgevoerd in een standaard analysebewerking. U geeft een set **VisualFeatureTypes-waarden** op, afhankelijk van uw behoeften. |

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken uitvoeren met de computervision-clientbibliotheek voor Python:

* [De client verifiëren](#authenticate-the-client)
* [Een afbeelding analyseren](#analyze-an-image)
* [Gedrukte en handgeschreven tekst lezen](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> Met deze quickstart wordt ervan uitgegaan dat u een `COMPUTER_VISION_SUBSCRIPTION_KEY` [omgevingsvariabele](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) hebt gemaakt voor uw Computer Vision-toets met de naam .

Instantiate een klant met uw eindpunt en sleutel. Maak een [object CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) met uw sleutel en gebruik het met uw eindpunt om een [ComputerVisionClient-object](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) te maken.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_client)]

## <a name="analyze-an-image"></a>Een afbeelding analyseren

Sla een verwijzing op naar de URL van een afbeelding die u wilt analyseren.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_remoteimage)]

### <a name="get-image-description"></a>Afbeeldingsbeschrijving downloaden

De volgende code krijgt de lijst met gegenereerde bijschriften voor de afbeelding. Zie [Afbeeldingen beschrijven](../../concept-describing-images.md) voor meer details.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_describe)]

### <a name="get-image-category"></a>Afbeeldingscategorie oppakken

De volgende code krijgt de gedetecteerde categorie van de afbeelding. Zie [Afbeeldingen categoriseren](../../concept-categorizing-images.md) voor meer informatie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_categorize)]

### <a name="get-image-tags"></a>Afbeeldingstags opmaken

De volgende code krijgt de set gedetecteerde tags in de afbeelding. Zie [Inhoudstags](../../concept-tagging-images.md) voor meer informatie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_tags)]

### <a name="detect-objects"></a>Objecten detecteren

De volgende code detecteert algemene objecten in de afbeelding en drukt deze af op de console. Zie [Objectdetectie](../../concept-object-detection.md) voor meer informatie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_objects)]

### <a name="detect-brands"></a>Merken detecteren

De volgende code detecteert bedrijfsmerken en logo's in de afbeelding en drukt deze af op de console. Zie [Merkdetectie](../../concept-brand-detection.md) voor meer details.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_brands)]

### <a name="detect-faces"></a>Gezichten detecteren

Met de volgende code worden de gedetecteerde gezichten in de afbeelding geretourneerd met hun rechthoekcoördinaten en selecteert u gezichtskenmerken. Zie [Gezichtsherkenning](../../concept-detecting-faces.md) voor meer details.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Detecteer inhoud voor volwassenen, racy of bloederige

Met de volgende code wordt de gedetecteerde aanwezigheid van inhoud voor volwassenen in de afbeelding afgedrukt. Zie [Adult, racy, bloederige inhoud](../../concept-detecting-adult-content.md) voor meer details.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Afbeeldingskleurenschema krijgen

In de volgende code worden de gedetecteerde kleurkenmerken in de afbeelding afgedrukt, zoals de dominante kleuren en accentkleur. Zie [Kleurenschema's](../../concept-detecting-color-schemes.md) voor meer informatie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Domeinspecifieke inhoud opmaken

Computer Vision kan gebruik maken van gespecialiseerde model om verdere analyse te doen op beelden. Zie [Domeinspecifieke inhoud](../../concept-detecting-domain-content.md) voor meer informatie. 

De volgende code ontleedt gegevens over gedetecteerde beroemdheden in de afbeelding.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_celebs)]

De volgende code onteert gegevens over gedetecteerde oriëntatiepunten in de afbeelding.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Het afbeeldingstype weergeven

In de volgende code wordt&mdash;informatie over het type afbeelding afgedrukt, ongeacht of het om illustraties of lijntekening gaat.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Gedrukte en handgeschreven tekst lezen

Computer Vision kan zichtbare tekst in een afbeelding lezen en converteren naar een tekenstroom. Je doet dit in twee delen.

### <a name="call-the-read-api"></a>De API lezen aanroepen

Gebruik eerst de volgende code om de **batch_read_file** methode voor de gegeven afbeelding aan te roepen. Hiermee wordt een bewerkings-id geretourneerd en wordt een asynchrone proces gestart om de inhoud van de afbeelding te lezen.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_call)]

### <a name="get-read-results"></a>Lees resultaten

Download vervolgens de bewerkings-id terug van de **batch_read_file** aanroepen en gebruik deze om de service op te vragen voor de bedrijfsresultaten. De volgende code controleert de bewerking met intervallen van één seconde totdat de resultaten zijn geretourneerd. Vervolgens worden de geëxtraheerde tekstgegevens naar de console afgedrukt.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_response)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing `python` uit met de opdracht voor uw quickstartbestand.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een abonnement voor Cognitive Services wilt opschonen en verwijderen, u de bron- of brongroep verwijderen. Als u de brongroep verwijdert, worden ook andere bronnen verwijderd die eraan zijn gekoppeld.

* [Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure-CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)


## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u de Computer Vision-bibliotheek voor Python gebruiken om basistaken uit te voeren. Bekijk vervolgens de referentiedocumentatie voor meer informatie over de bibliotheek.


> [!div class="nextstepaction"]
>[Verwijzing naar computervision-API (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)

* [Wat is Computer Vision?](../../Home.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py)
