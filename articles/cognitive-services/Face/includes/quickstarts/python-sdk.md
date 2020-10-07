---
title: Quickstart voor Face Python-clientbibliotheek
description: Gebruik de Face-clientbibliotheek voor Python om gezichten te detecteren, gelijksoortige gezichten te zoeken (gezichten zoeken op afbeelding), gezichten te identificeren (zoeken met gezichtsherkenning) en uw gezichtsgegevens te migreren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 09/17/2020
ms.author: pafarley
ms.openlocfilehash: f746a61850567014ce216c47df472d035f1ae123
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322948"
---
Ga aan de slag met gezichtsherkenning met behulp van de Face-clientbibliotheek voor Python. Volg deze stappen om het pakket te installeren en de voorbeeldcode voor basistaken uit te proberen. De Face-service biedt u toegang tot geavanceerde algoritmen voor het detecteren en herkennen van menselijke gezichten in afbeeldingen.

Gebruik de Face-clientbibliotheek voor Python voor het volgende:

* Gezichten in een afbeelding detecteren
* Vergelijkbare gezichten zoeken
* Een persoonsgroep maken en trainen
* Een gezicht identificeren
* Gezichten verifiëren
* Een momentopname maken voor gegevensmigratie

[Referentiedocumentatie](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python) | [Broncode bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face) | [Package (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-face/) | [Voorbeelden](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Vereisten

* [Python 3.x](https://www.python.org/)
* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* Zodra u een Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Een Face-resource maken"  target="_blank">maakt u een Face-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in Azure Portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Face-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.
* Nadat u een sleutel en eindpunt hebt verkregen, gaat u [omgevingsvariabelen maken](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de sleutel en het eindpunt, respectievelijk `FACE_SUBSCRIPTION_KEY` en `FACE_ENDPOINT` genaamd.

## <a name="setting-up"></a>Instellen
 
### <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

Maak bijvoorbeeld een nieuw Python-script&mdash;*quickstart-file.py*. Open vervolgens het bestand in uw voorkeurseditor of IDE en importeer de volgende bibliotheken.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

Maak dan variabelen voor het Azure-eindpunt en de Azure-sleutel voor uw resource.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!NOTE]
> Als u de omgevingsvariabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell waarmee deze wordt uitgevoerd, sluiten en opnieuw openen om toegang te krijgen tot de variabele.

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

U kunt de clientbibliotheek installeren met:

```console
pip install --upgrade azure-cognitiveservices-vision-face
```

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Face Python-clientbibliotheek:

|Naam|Beschrijving|
|---|---|
|[FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) | Deze klasse vertegenwoordigt uw autorisatie voor het gebruik van de Face-service. U hebt deze nodig voor alle Face-functies. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om instanties van andere klassen te maken. |
|[FaceOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python)|Deze klasse verwerkt de basistaken voor detectie en herkenning die u met menselijke gezichten kunt uitvoeren. |
|[DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)|Deze klasse vertegenwoordigt alle gegevens die zijn gedetecteerd van één gezicht in een afbeelding. U kunt deze gebruiken om gedetailleerde informatie over het gezicht op te halen.|
|[FaceListOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations?view=azure-python)|Deze klasse beheert de in de cloud opgeslagen **FaceList**-constructies, waarin een geassorteerde set gezichten wordt opgeslagen. |
|[PersonGroupPersonOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations?view=azure-python)| Deze klasse beheert de in de cloud opgeslagen **Person**-constructies, die een set gezichten opslaan die tot één persoon behoren.|
|[PersonGroupOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations?view=azure-python)| Deze klasse beheert de in de cloud opgeslagen **PersonGroup**-constructies, die een set van verschillende **Person**-objecten opslaan. |
|[ShapshotOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations?view=azure-python)|Deze klasse beheert de functionaliteit van de momentopname. U kunt deze gebruiken om al uw op de cloud gebaseerde gezichtsgegevens tijdelijk op te slaan en deze gegevens te migreren naar een nieuw Azure-abonnement. |

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Face-clientbibliotheek voor Python:

* [De client verifiëren](#authenticate-the-client)
* [Gezichten in een afbeelding detecteren](#detect-faces-in-an-image)
* [Vergelijkbare gezichten zoeken](#find-similar-faces)
* [Een persoonsgroep maken en trainen](#create-and-train-a-person-group)
* [Een gezicht identificeren](#identify-a-face)
* [Gezichten verifiëren](#verify-faces)
* [Een momentopname maken voor gegevensmigratie](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> In deze quickstart wordt ervan uitgegaan dat u [een omgevingsvariabele hebt gemaakt](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) voor uw Face-sleutel, met de naam `FACE_SUBSCRIPTION_KEY`.

Instantieer een client met uw eindpunt en sleutel. Maak een [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python)-object met uw sleutel en gebruik het met uw eindpunt om een [FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python)-object te maken.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>Gezichten in een afbeelding detecteren

De volgende code detecteert een gezicht in een externe afbeelding. Hiermee wordt de id van het gedetecteerde gezicht afgedrukt naar de console en wordt in het programmageheugen opgeslagen. Vervolgens worden de gezichten van een afbeelding met meerdere personen gedetecteerd en worden de bijbehorende id's ook naar de console afgedrukt. Door de parameters in de methode [detect_with_url](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-) te wijzigen, kunt u verschillende gegevens retourneren met elk [DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)-object.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

Zie de voorbeeldcode op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py) voor meer detectiescenario's.

### <a name="display-and-frame-faces"></a>Gezichten weergeven en kaderen

Met de volgende code wordt de opgegeven afbeelding naar het scherm uitgevoerd en worden rechthoeken rond de vlakken getekend met behulp van de eigenschap DetectedFace.faceRectangle.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_frame)]

![Een jonge vrouw met een rode rechthoek rond haar gezicht](../../images/face-rectangle-result.png)

## <a name="find-similar-faces"></a>Vergelijkbare gezichten zoeken

Met de volgende code wordt op basis van één gedetecteerd gezicht (bron) een reeks andere gezichten (doel) doorzocht om een overeenkomstig gezicht te vinden (gezichten zoeken op afbeelding). Wanneer er een overeenkomst wordt gevonden, wordt de id van het overeenkomende gezicht afgedrukt op de console.

### <a name="find-matches"></a>Overeenkomsten zoeken

Voer eerst de code uit in de bovenstaande sectie ([Gezichten in een afbeelding detecteren](#detect-faces-in-an-image)) om een verwijzing naar één gezicht op te slaan. Voer vervolgens de volgende code uit om verwijzingen naar verschillende gezichten in een groepsafbeelding te krijgen.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

Voeg daarna het volgende codeblok toe om exemplaren van het eerste gezicht in de groep te zoeken. Raadpleeg de methode [find_similar](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) voor meer informatie over hoe u dit gedrag kunt wijzigen.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>Overeenkomsten afdrukken

Met de volgende code worden de overeenkomende resultaten op de console weergegeven:

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-person-group"></a>Een persoonsgroep maken en trainen

Met de volgende code wordt een **PersonGroup** gemaakt met drie verschillende **Person**-objecten. Het koppelt elke **persoon** aan een reeks voorbeeldafbeeldingen en traint vervolgens om elke persoon te herkennen. 

### <a name="create-persongroup"></a>PersonGroup maken

Als u dit scenario wilt doorlopen, moet u de volgende afbeeldingen opslaan in de hoofdmap van uw project: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

Deze groep met afbeeldingen bevat drie sets gezichtsafbeeldingen die overeenkomen met drie verschillende personen. Met de code worden drie **Person**-objecten gedefinieerd en gekoppeld aan afbeeldingsbestanden die beginnen met `woman`, `man`en `child`.

Nadat u uw afbeeldingen hebt ingesteld, definieert u een label bovenaan het script voor het **PersonGroup**-object dat u maakt.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

Voeg onderaan uw script de volgende code toe. Met deze code maakt u een **PersonGroup** en drie **Person**-objecten.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>Gezichten toewijzen aan personen

Met de volgende code worden uw afbeeldingen gesorteerd op basis van het voorvoegsel, worden de gezichten gedetecteerd en worden de gezichten toegewezen aan elk **Person**-object.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

### <a name="train-persongroup"></a>PersonGroup trainen

Zodra u gezichten hebt toegewezen, moet u de **PersonGroup** trainen zodat deze de visuele functies kan identificeren die zijn gekoppeld aan elk van de **Person**-objecten. Met de volgende code wordt de asynchrone **Train**-methode aangeroepen en worden de resultaten gecontroleerd, en wordt de status naar de console afgedrukt.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

## <a name="identify-a-face"></a>Een gezicht identificeren

Bij de bewerking Identificeren wordt op basis van een afbeelding van een persoon (of meerdere personen) gezocht naar de identiteit van elk gezicht in de afbeelding (zoeken met gezichtsherkenning). Elk gedetecteerd gezicht wordt vergeleken met een **PersonGroup**, een database van verschillende **Person**-objecten waarvan de gezichtskenmerken bekend zijn.

> [!IMPORTANT]
> Als u dit voorbeeld wilt uitvoeren, moet u eerst de code uitvoeren in [Een persoonsgroep maken en trainen](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Een testafbeelding ophalen

De volgende code zoekt in de hoofdmap van uw project naar een afbeelding _test-image-person-group. jpg_ en detecteert de gezichten in de afbeelding. U kunt deze afbeelding vinden met de afbeeldingen die worden gebruikt voor **PersonGroup**-beheer: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>Gezichten identificeren

De methode **Identificeren** gebruikt een matrix met gedetecteerde gezichten en vergelijkt deze met een **PersonGroup**. Als een gedetecteerd gezicht overeenkomt met een **Person**, wordt het resultaat opgeslagen. Met deze code worden gedetailleerde overeenkomende resultaten naar de console afgedrukt.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify)]

## <a name="verify-faces"></a>Gezichten verifiëren

De bewerking Verifiëren neemt een gezichts-id en een andere gezichts-id of een **Person**-object en bepaalt of deze van dezelfde persoon zijn.

De volgende code detecteert gezichten in twee bronafbeeldingen en vergelijkt deze vervolgens met een gezicht dat is gedetecteerd in een doelafbeelding.

### <a name="get-test-images"></a>Testafbeeldingen ophalen

De volgende codeblokken declareren variabelen die verwijzen naar de bron- en doelafbeeldingen voor de verificatiebewerking.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_baseurl)]

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_photos)]

### <a name="detect-faces-for-verification"></a>Gezichten voor verificatie detecteren

De volgende code detecteert gezichten in de bron- en doelafbeeldingen en slaat ze op in variabelen.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_detect)]

### <a name="get-verification-results"></a>Verificatieresultaten ophalen

De volgende code vergelijkt elk van de bronafbeeldingen met de doelafbeelding en drukt een bericht af dat aangeeft of ze van dezelfde persoon zijn.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify)]

## <a name="take-a-snapshot-for-data-migration"></a>Een momentopname maken voor gegevensmigratie

Met de functie Momentopnamen kunt u opgeslagen gezichtsgegevens, zoals een getrainde **PersonGroup**, verplaatsen naar een ander Azure Cognitive Services Face-abonnement. U kunt deze functie gebruiken als u bijvoorbeeld een **PersonGroup**-object hebt gemaakt met behulp van een gratis abonnement en wilt migreren naar een betaald abonnement. Zie [Uw gezichtsgegevens migreren](../../Face-API-How-to-Topics/how-to-migrate-face-data.md) voor een overzicht van de functie momentopnamen.

In dit voorbeeld migreert u de **PersonGroup** die u hebt gemaakt in [Een groep personen maken en trainen](#create-and-train-a-person-group). U kunt deze sectie eerst voltooien of uw eigen Face-gegevensconstructies gebruiken.

### <a name="set-up-target-subscription"></a>Doelabonnement instellen

Eerst moet u een tweede Azure-abonnement hebben met een Face-resource. U kunt dit doen door de stappen te volgen in het gedeelte [Instellen](#setting-up). 

Maak vervolgens de volgende variabelen bovenaan uw script. U moet nieuwe omgevingsvariabelen maken voor de abonnements-id van uw Azure-account, evenals de sleutel-, eindpunt- en abonnements-id van uw nieuwe (doel)account. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshotvars)]

### <a name="authenticate-target-client"></a>Doelclient verifiëren

Sla later in uw script uw ​​huidige clientobject op als bronclient en verifieer vervolgens een nieuw clientobject voor uw doelabonnement. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_auth)]

### <a name="use-a-snapshot"></a>Een momentopname gebruiken

De rest van de momentopnamebewerkingen vinden plaats binnen een asynchrone functie. 

1. De eerste stap is het **nemen van** de momentopname, waarmee de gezichtsgegevens van uw oorspronkelijke abonnement worden opgeslagen op een tijdelijke locatie van de Cloud. Deze methode retourneert een id die u gebruikt om de status van de bewerking op te vragen.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_take)]

1. Vervolgens moet u een query uitvoeren op de id totdat de bewerking is voltooid.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait)]

    Deze code maakt gebruik van de functie `wait_for_operation`, die u apart moet definiëren:

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_waitforop)]

1. Ga terug naar uw asynchrone functie. Gebruik vervolgens de bewerking **Toepassen** om uw gezichtsgegevens te schrijven naar uw doelabonnement. Met deze methode wordt ook een id geretourneerd.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_apply)]

1. Gebruik nogmaals de functie `wait_for_operation` om de id op te vragen totdat de bewerking is voltooid.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait2)]

Zodra u deze stappen hebt voltooid, hebt u toegang tot uw gezichtsgegevens van uw nieuwe (doel)abonnement.

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer uw gezichtsherkennings-app uit vanuit de toepassingsmap met de opdracht `python`.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure-CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)

Als u in deze quickstart een **PersonGroup** hebt gemaakt en u deze wilt verwijderen, voert u de volgende code uit in uw script:

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

Als u gegevens hebt gemigreerd met behulp van de momentopnamefunctie in deze quickstart, moet u ook de **PersonGroup** verwijderen die is opgeslagen in het doelabonnement.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletetargetgroup)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u de Face-clientbibliotheek voor Python gebruikt om basistaken voor gezichtsherkenning uit te voeren. Bestudeer daarna het naslagmateriaal bij de Face-API voor meer informatie.

> [!div class="nextstepaction"]
> [Verwijzing naar de Face-API (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)

* [Wat is de Face-service?](../../overview.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py).
