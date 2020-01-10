---
title: 'Snelstartgids: Face client-bibliotheek voor python'
description: Deze Snelstartgids helpt u om aan de slag te gaan met de face-client bibliotheek voor python om te detecteren, vergelijk bare, identificeren, controleren en meer te vinden.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: badfec71ad44fd9eebc748f716301ad6731883cf
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770353"
---
# <a name="quickstart-face-client-library-for-python"></a>Snelstartgids: Face client-bibliotheek voor python

Ga aan de slag met de face-client bibliotheek voor python. Volg deze stappen om het pakket te installeren en de voorbeeld code voor basis taken uit te proberen. De Face-API-service biedt u toegang tot geavanceerde algoritmen voor het detecteren en herkennen van menselijke gezichten in afbeeldingen.

Gebruik de face client-bibliotheek voor python voor het volgende:

* Gezichten in een afbeelding detecteren
* Vergelijkbare gezichten zoeken
* Een persoons groep maken en trainen
* Een gezicht identificeren
* Gezichten verifiëren
* Een moment opname maken voor gegevens migratie

[Referentie documentatie](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python) | - [bibliotheek bron code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face) | [pakket (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-face/) | -voor [beelden](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Instellen

### <a name="create-a-face-azure-resource"></a>Een gezichts-Azure-resource maken

Azure-Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een resource voor gezicht met behulp van de [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure cli](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine. U kunt ook het volgende doen:

* Ontvang een [proef sleutel](https://azure.microsoft.com/try/cognitive-services/#decision) die zeven dagen gratis geldig is. Nadat u zich hebt aangemeld, is deze beschikbaar op de [Azure-website](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Bekijk uw resource op het [Azure Portal](https://portal.azure.com/)

Nadat u een sleutel van uw proef abonnement of resource hebt ontvangen, [maakt u een omgevings variabele](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de sleutel met de naam `FACE_SUBSCRIPTION_KEY`.
 
### <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

Maak een nieuw python-script&mdash;*QuickStart-file.py*, bijvoorbeeld. Open het vervolgens in uw voorkeurs editor of IDE en importeer de volgende bibliotheken.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

Maak vervolgens variabelen voor het Azure-eind punt en de sleutel van uw resource. Mogelijk moet u het eerste deel van het eind punt (`westus`) wijzigen zodat dit overeenkomt met uw abonnement.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!NOTE]
> Als u de omgevings variabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell waarmee deze wordt uitgevoerd, sluiten en opnieuw openen om toegang te krijgen tot de variabele.

### <a name="install-the-client-library"></a>De client bibliotheek installeren

U kunt de client bibliotheek installeren met:

```console
pip install --upgrade azure-cognitiveservices-vision-face
```

## <a name="object-model"></a>Object model

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de face python SDK.

|Name|Beschrijving|
|---|---|
|[FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) | Deze klasse vertegenwoordigt uw autorisatie voor het gebruik van de face-service en u hebt deze nodig voor alle gezichts functionaliteit. U maakt de app met uw abonnements gegevens en gebruikt deze om instanties van andere klassen te maken. |
|[FaceOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python)|Deze klasse verwerkt de basis taken voor detectie en herkenning die u met menselijke gezichten kunt uitvoeren. |
|[DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)|Deze klasse vertegenwoordigt alle gegevens die zijn gedetecteerd van één gezicht in een afbeelding. U kunt deze gebruiken om gedetailleerde informatie over het gezicht op te halen.|
|[FaceListOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations?view=azure-python)|Deze klasse beheert de in de cloud opgeslagen **FaceList** -constructies, waarin een geassorteerde set gezichten wordt opgeslagen. |
|[PersonGroupPersonOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations?view=azure-python)| Deze klasse beheert de door de cloud opgeslagen **persoons** constructies, waarin een set gezichten wordt opgeslagen die deel uitmaakt van één persoon.|
|[PersonGroupOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations?view=azure-python)| Deze klasse beheert de in de cloud opgeslagen **PersonGroup** -constructies, die een set geassorteerde **persoons** objecten opslaan. |
|[ShapshotOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations?view=azure-python)|Deze klasse beheert de functionaliteit van de moment opname. u kunt deze gebruiken om al uw op de cloud gebaseerde gezichts gegevens tijdelijk op te slaan en deze gegevens te migreren naar een nieuw Azure-abonnement. |

## <a name="code-examples"></a>Codevoorbeelden

Deze code fragmenten laten zien hoe u de volgende taken kunt uitvoeren met de face-client bibliotheek voor python:

* [De client verifiëren](#authenticate-the-client)
* [Gezichten detecteren in een installatie kopie](#detect-faces-in-an-image)
* [Vergelijk bare gezichten zoeken](#find-similar-faces)
* [Een persoons groep maken en trainen](#create-and-train-a-person-group)
* [Een gezicht identificeren](#identify-a-face)
* [Gezichten verifiëren](#verify-faces)
* [Een moment opname maken voor gegevens migratie](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> In deze Quick Start wordt ervan uitgegaan dat u [een omgevings variabele hebt gemaakt](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) voor uw gezichts sleutel met de naam `FACE_SUBSCRIPTION_KEY`.

Exemplaar een client met uw eind punt en sleutel. Maak een [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) -object met uw sleutel en gebruik het met uw eind punt om een [FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) -object te maken.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>Gezichten in een afbeelding detecteren

Met de volgende code wordt een gezicht gedetecteerd in een externe installatie kopie. Hiermee wordt de ID van het gedetecteerde gezicht afgedrukt op de-console en wordt deze ook opgeslagen in het programma geheugen. Vervolgens worden de gezichten van een afbeelding met meerdere personen gedetecteerd en worden de bijbehorende Id's ook naar de console afgedrukt. Door de para meters in de methode [detect_with_url](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-) te wijzigen, kunt u verschillende informatie met elk [DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python) -object retour neren.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

Zie de voorbeeld code op [github](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py) voor meer detectie scenario's.

### <a name="display-and-frame-faces"></a>Weer gave-en frame gezichten

Met de volgende code wordt de opgegeven afbeelding op het scherm weer gegeven en worden rechthoeken rond de gezichten getekend, met behulp van de eigenschap DetectedFace. faceRectangle.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_frame)]

![Een jonge vrouw met een rode rechthoek rond haar gezicht](../images/face-rectangle-result.png)

## <a name="find-similar-faces"></a>Vergelijkbare gezichten zoeken

De volgende code neemt één gedetecteerd gezicht en doorzoekt een set andere gezichten om overeenkomsten te vinden. Wanneer er een overeenkomst wordt gevonden, worden de rechthoek coördinaten van het overeenkomende gezicht naar de console afgedrukt. 

### <a name="find-matches"></a>Overeenkomsten zoeken

Voer eerst de code in de bovenstaande sectie ([gezichten detecteren in een afbeelding](#detect-faces-in-an-image)) uit om een verwijzing naar één gezicht op te slaan. Voer vervolgens de volgende code uit om verwijzingen naar meerdere gezichten in een groeps installatie kopie op te halen.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

Voeg vervolgens het volgende code blok toe om exemplaren van het eerste gezicht in de groep te vinden. Raadpleeg de [find_similar](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) -methode om te leren hoe u dit gedrag kunt wijzigen.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>Overeenkomsten afdrukken

Gebruik de volgende code om de overeenkomende gegevens af te drukken naar de-console.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-person-group"></a>Een persoons groep maken en trainen

Met de volgende code wordt een **PersonGroup** gemaakt met drie verschillende **personen** -objecten. Elke **persoon** wordt gekoppeld aan een set voorbeeld afbeeldingen en vervolgens wordt de treinen in staat gesteld om elke persoon te herkennen. 

### <a name="create-persongroup"></a>PersonGroup maken

Als u dit scenario wilt door lopen, moet u de volgende installatie kopieën opslaan in de hoofd directory van uw project: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

Deze groep met installatie kopieën bevat drie sets gezichts afbeeldingen die overeenkomen met drie verschillende personen. Met de code worden drie **persoons** objecten gedefinieerd en gekoppeld aan afbeeldings bestanden die beginnen met `woman`, `man`en `child`.

Nadat u uw installatie kopieën hebt ingesteld, definieert u een label boven aan het script voor het **PersonGroup** -object dat u maakt.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

Voeg vervolgens de volgende code toe aan de onderkant van het script. Deze code maakt een **PersonGroup** -en drie **persoons** -objecten.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>Gezichten toewijzen aan personen

Met de volgende code worden uw afbeeldingen gesorteerd op basis van het voor voegsel, worden gezichten gedetecteerd en worden de gezichten aan elk **persoons** object toegewezen.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

### <a name="train-persongroup"></a>PersonGroup trainen

Zodra u gezichten hebt toegewezen, moet u de **PersonGroup** trainen zodat deze de visuele functies kan identificeren die zijn gekoppeld aan elk van de objecten van de **persoon** . Met de volgende code wordt de asynchrone **trein** methode aangeroepen en wordt het resultaat gecontroleerd en wordt de status afgedrukt naar de-console.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

## <a name="identify-a-face"></a>Een gezicht identificeren

De volgende code neemt een afbeelding met meerdere gezichten en zoekt naar de identiteit van elke persoon in de installatie kopie. Elk gedetecteerd gezicht wordt vergeleken met een **PersonGroup**, een Data Base van verschillende **personen** -objecten waarvan de gezichts kenmerken bekend zijn.

> [!IMPORTANT]
> Als u dit voor beeld wilt uitvoeren, moet u eerst de code uitvoeren in [een persoons groep maken en trainen](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Een test installatie kopie ophalen

De volgende code zoekt in de hoofdmap van het project naar een afbeelding _Test-Image-person-Group. jpg_ en detecteert de gezichten in de installatie kopie. U kunt deze installatie kopie vinden met de installatie kopieën die worden gebruikt voor **PersonGroup** management: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>Gezichten identificeren

De methode **Identify** gebruikt een matrix met gedetecteerde gezichten en vergelijkt deze met een **PersonGroup**. Als de app een gedetecteerd gezicht kan overeenkomen met een **persoon**, wordt het resultaat opgeslagen. Met deze code worden gedetailleerde resultaat resultaten in de-console afgedrukt.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify)]

## <a name="verify-faces"></a>Gezichten verifiëren

Bij de controle bewerking wordt een face-ID en een andere gezichts-ID of een **persoons** object gebruikt en wordt bepaald of deze deel uitmaken van dezelfde persoon.

De volgende code detecteert gezichten in twee bron afbeeldingen en controleert deze vervolgens op een gezicht dat is gedetecteerd vanuit een doel installatie kopie.

### <a name="get-test-images"></a>Test installatie kopieën ophalen

De volgende code blokkeert het declareren van variabelen die verwijzen naar de bron-en doel installatie kopieën voor de verificatie bewerking.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_baseurl)]

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_photos)]

### <a name="detect-faces-for-verification"></a>Gezichten voor verificatie detecteren

De volgende code detecteert gezichten in de bron-en doel installatie kopieën en slaat ze op in variabelen.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_detect)]

### <a name="get-verification-results"></a>Resultaten van de verificatie ophalen

De volgende code vergelijkt elk van de bron installatie kopieën met de doel installatie kopie en drukt een bericht af dat aangeeft of ze tot dezelfde persoon behoren.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify)]

## <a name="take-a-snapshot-for-data-migration"></a>Een moment opname maken voor gegevens migratie

Met de functie moment opnamen kunt u opgeslagen gezichts gegevens, zoals een getraind **PersonGroup**, verplaatsen naar een ander Azure Cognitive Services Face-abonnement. U kunt deze functie gebruiken als u bijvoorbeeld een **PersonGroup** -object hebt gemaakt met een gratis proef abonnement en nu wilt migreren naar een betaald abonnement. Zie de [informatie over uw gezicht migreren](../Face-API-How-to-Topics/how-to-migrate-face-data.md) voor een uitgebreid overzicht van de functie moment opnamen.

In dit voor beeld migreert u de **PersonGroup** die u hebt gemaakt in [een persoons groep maken en trainen](#create-and-train-a-person-group). U kunt deze sectie eerst volt ooien of uw eigen face data-construct (s) gebruiken.

### <a name="set-up-target-subscription"></a>Doel abonnement instellen

Eerst moet u een tweede Azure-abonnement hebben met een face-resource. u kunt dit doen door de stappen in de sectie [instellen](#setting-up) te volgen. 

Maak vervolgens de volgende variabelen aan de bovenkant van uw script. U moet ook nieuwe omgevings variabelen maken voor de abonnements-ID van uw Azure-account, evenals de sleutel, het eind punt en de abonnements-ID van uw nieuwe (doel) account. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshotvars)]

### <a name="authenticate-target-client"></a>Doel-client verifiëren

Sla het huidige client object later in uw script op als de bron-client en Verifieer vervolgens een nieuw client object voor uw doel abonnement. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_auth)]

### <a name="use-a-snapshot"></a>Een moment opname gebruiken

De rest van de momentopname bewerkingen worden uitgevoerd binnen een asynchrone functie. 

1. De eerste stap is het **maken** van de moment opname, waarmee de gezichts gegevens van uw oorspronkelijke abonnement worden opgeslagen op een tijdelijke locatie van de Cloud. Deze methode retourneert een ID die u gebruikt om de status van de bewerking op te vragen.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_take)]

1. Vervolgens moet u een query uitvoeren op de ID totdat de bewerking is voltooid.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait)]

    Deze code maakt gebruik van de functie `wait_for_operation`, die u afzonderlijk moet definiëren:

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_waitforop)]

1. Ga terug naar uw asynchrone functie. Gebruik de bewerking **Apply** om uw gezichts gegevens te schrijven naar uw doel abonnement. Met deze methode wordt ook een ID geretourneerd.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_apply)]

1. Gebruik opnieuw de `wait_for_operation` functie om de ID op te vragen totdat de bewerking is voltooid.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait2)]

Zodra u deze stappen hebt voltooid, kunt u toegang krijgen tot uw gezichts gegevens constructies van uw nieuwe (doel) abonnement.

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de opdracht `python` in uw Quick Start-bestand.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure-CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)

Als u in deze Quick Start een **PersonGroup** hebt gemaakt en u deze wilt verwijderen, voert u de volgende code in uw script uit:

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

Als u gegevens hebt gemigreerd met behulp van de snap shot-functie in deze Quick Start, moet u ook de **PersonGroup** verwijderen die is opgeslagen in het doel abonnement.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletetargetgroup)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u de face-bibliotheek voor python kunt gebruiken om basis taken uit te voeren. Bekijk vervolgens de referentie documentatie voor meer informatie over de-bibliotheek.

> [!div class="nextstepaction"]
> [Face-API verwijzing (python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)

* [Wat is de Face-API?](../overview.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py).
