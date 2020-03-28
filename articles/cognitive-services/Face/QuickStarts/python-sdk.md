---
title: 'Snelstart: Face-clientbibliotheek voor Python'
description: Met deze quickstart u aan de slag met de Face-clientbibliotheek voor Python om vergelijkbare gegevens op te sporen, te identificeren, te verifiëren en meer.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 7ac1b85ee44627990931cfc7b3a65f6f7a661b3f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76165820"
---
# <a name="quickstart-face-client-library-for-python"></a>Snelstart: Face-clientbibliotheek voor Python

Ga aan de slag met de Face-clientbibliotheek voor Python. Volg deze stappen om het pakket te installeren en probeer de voorbeeldcode voor basistaken uit. De Face-service biedt u toegang tot geavanceerde algoritmen voor het detecteren en herkennen van menselijke gezichten in afbeeldingen.

Gebruik de Face-clientbibliotheek voor Python om:

* Gezichten in een afbeelding detecteren
* Vergelijkbare gezichten zoeken
* Een personengroep maken en trainen
* Een gezicht identificeren
* Gezichten verifiëren
* Een momentopname maken voor gegevensmigratie

[Voorbeeld van broncode](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python) |  | van[naslagdocumentatieBibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face)[(PiPy)-voorbeelden](https://pypi.org/project/azure-cognitiveservices-vision-face/) | [Samples](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Instellen

### <a name="create-a-face-azure-resource"></a>Een Face Azure-bron maken

Azure Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een bron voor Face met de [Azure-portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine. U kunt ook het volgende doen:

* Ontvang een [proefsleutel](https://azure.microsoft.com/try/cognitive-services/#decision) die zeven dagen gratis geldig is. Nadat u zich hebt aangemeld, is deze beschikbaar op de [Azure-website.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Uw bron weergeven op de [Azure-portal](https://portal.azure.com/)

Nadat u een sleutel uit uw proefabonnement of resource hebt opgehaald, [maakt u een omgevingsvariabele](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de sleutel met de naam `FACE_SUBSCRIPTION_KEY`.
 
### <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

Maak bijvoorbeeld een&mdash;nieuw Python-script*quickstart-file.py.* Open deze vervolgens in uw voorkeurseditor of IDE en importeer de volgende bibliotheken.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

Maak vervolgens variabelen voor het Azure-eindpunt en de sleutel van uw resource. Mogelijk moet u het eerste deel van`westus`het eindpunt () aanpassen aan uw abonnement.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!NOTE]
> Als u de omgevingsvariabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell sluiten en opnieuw openen om toegang te krijgen tot de variabele.

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

U de clientbibliotheek installeren met:

```console
pip install --upgrade azure-cognitiveservices-vision-face
```

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces behandelen enkele van de belangrijkste functies van de Face Python SDK.

|Name|Beschrijving|
|---|---|
|[FaceClient (FaceClient)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) | Deze klasse vertegenwoordigt uw autorisatie om de Face-service te gebruiken en u hebt deze nodig voor alle Face-functionaliteit. U wilt deze met uw abonnementsgegevens en u gebruikt deze om exemplaren van andere klassen te produceren. |
|[FaceOperations (FaceOperations)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python)|Deze klasse behandelt de basisdetectie- en herkenningstaken die u uitvoeren met menselijke gezichten. |
|[GedetecteerdGezicht](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)|Deze klasse vertegenwoordigt alle gegevens die zijn gedetecteerd vanaf één gezicht in een afbeelding. U het gebruiken om gedetailleerde informatie over het gezicht op te halen.|
|[FaceListOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations?view=azure-python)|Deze klasse beheert de door de cloud opgeslagen **FaceList-constructies,** die een diverse set gezichten opslaan. |
|[PersonGroupPersonOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations?view=azure-python)| Deze klasse beheert de **Person** door de cloud opgeslagen persoonsconstructies, die een reeks gezichten opslaan die van één persoon zijn.|
|[PersonGroupOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations?view=azure-python)| Deze klasse beheert de door de cloud opgeslagen **PersonGroup-constructies,** waarmee een set diverse **persoonsobjecten wordt** opgeslagen. |
|[ShapshotOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations?view=azure-python)|Deze klasse beheert de snapshot-functionaliteit; u deze gebruiken om al uw gezichtsgegevens in de cloud tijdelijk op te slaan en die gegevens te migreren naar een nieuw Azure-abonnement. |

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken uitvoeren met de Face-clientbibliotheek voor Python:

* [De client verifiëren](#authenticate-the-client)
* [Gezichten in een afbeelding detecteren](#detect-faces-in-an-image)
* [Vergelijkbare gezichten zoeken](#find-similar-faces)
* [Een personengroep maken en trainen](#create-and-train-a-person-group)
* [Een gezicht identificeren](#identify-a-face)
* [Gezichten verifiëren](#verify-faces)
* [Een momentopname maken voor gegevensmigratie](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> Met deze quickstart wordt ervan uitgegaan dat u een `FACE_SUBSCRIPTION_KEY` [omgevingsvariabele](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) hebt gemaakt voor uw Face-toets met de naam .

Instantiate een klant met uw eindpunt en sleutel. Maak een [Object CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) met uw sleutel en gebruik het met uw eindpunt om een [FaceClient-object](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) te maken.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>Gezichten in een afbeelding detecteren

De volgende code detecteert een gezicht in een externe afbeelding. Het drukt de gedetecteerde gezicht ID op de console en slaat het ook op in het programma geheugen. Vervolgens detecteert het de gezichten in een afbeelding met meerdere mensen en drukt hun id's ook op de console. Door de parameters in de [detect_with_url](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-) methode te wijzigen, u verschillende informatie retourneren met elk [DetectedFace-object.](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

Zie de voorbeeldcode op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py) voor meer detectiescenario's.

### <a name="display-and-frame-faces"></a>Vlakken en kadervlakken weergeven

Met de volgende code wordt de gegeven afbeelding naar de weergave uitgevoerd en worden rechthoeken rond de vlakken gemaakt met de eigenschap DetectedFace.faceRectangle.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_frame)]

![Een jonge vrouw met een rode rechthoek rond haar gezicht](../images/face-rectangle-result.png)

## <a name="find-similar-faces"></a>Vergelijkbare gezichten zoeken

De volgende code neemt een enkel gedetecteerd gezicht en zoekt een set andere gezichten om overeenkomsten te vinden. Wanneer een overeenkomst wordt gevonden, worden de rechthoekcoördinaten van het overeenkomende gezicht afgedrukt op de console. 

### <a name="find-matches"></a>Zoek overeenkomsten

Voer eerst de code uit in de bovenstaande sectie[(Gezichten detecteren in een afbeelding)](#detect-faces-in-an-image)om een verwijzing naar één gezicht op te slaan. Voer vervolgens de volgende code uit om verwijzingen naar verschillende gezichten in een groepsafbeelding te krijgen.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

Voeg vervolgens het volgende codeblok toe om exemplaren van het eerste gezicht in de groep te zoeken. Zie de [find_similar](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) methode om te leren hoe u dit gedrag wijzigen.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>Overeenkomen afdrukken

Gebruik de volgende code om de overeenkomende gegevens af te drukken op de console.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-person-group"></a>Een personengroep maken en trainen

Met de volgende code wordt een **persoonsgroep gemaakt** met drie verschillende **persoonsobjecten.** Het associeert elke **persoon** met een reeks voorbeeldafbeeldingen, en vervolgens traint het om elke persoon te kunnen herkennen. 

### <a name="create-persongroup"></a>Persoonsgroep maken

Als u dit scenario wilt doorlopen, moet u de volgende https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/imagesafbeeldingen opslaan in de hoofdmap van uw project: .

Deze groep afbeeldingen bevat drie sets gezichtsafbeeldingen die overeenkomen met drie verschillende personen. De code definieert drie **persoonsobjecten** en koppelt `woman` `man`ze `child`aan afbeeldingsbestanden die beginnen met , en .

Zodra u uw afbeeldingen hebt ingesteld, definieert u een label boven aan uw script voor het object **PersonGroup** dat u maakt.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

Voeg vervolgens de volgende code toe aan de onderkant van het script. Met deze code worden een **persoonsgroep** en drie **persoonsobjecten** gemaakt.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>Gezichten toewijzen aan personen

Met de volgende code worden uw afbeeldingen op hun voorvoegsel gekert, worden gezichten gedetecteerd en worden de vlakken aan elk **object persoon** toewijst.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

### <a name="train-persongroup"></a>Trein persoonsgroep

Zodra u gezichten hebt toegewezen, moet u de **groep persoon** trainen zodat deze de visuele functies kan identificeren die zijn gekoppeld aan elk van de **persoonsobjecten.** De volgende code roept de asynchrone **treinmethode** aan en peilt het resultaat en drukt de status af op de console.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

## <a name="identify-a-face"></a>Een gezicht identificeren

De volgende code neemt een afbeelding met meerdere gezichten en kijkt naar de identiteit van elke persoon in de afbeelding te vinden. Het vergelijkt elk gedetecteerd gezicht met een **PersonGroup**, een database van verschillende **persoonobjecten** waarvan de gelaatstrekken bekend zijn.

> [!IMPORTANT]
> Als u dit voorbeeld wilt uitvoeren, moet u eerst de code uitvoeren in [Een personengroep maken en trainen.](#create-and-train-a-person-group)

### <a name="get-a-test-image"></a>Een testafbeelding krijgen

De volgende code zoekt in de hoofdmap van uw project naar een _afbeeldingstest-image-person-group.jpg_ en detecteert de gezichten in de afbeelding. U deze afbeelding vinden met de https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/imagesafbeeldingen die worden gebruikt voor **persongroup-beheer:** .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>Gezichten identificeren

De **identificatiemethode** neemt een array van gedetecteerde gezichten en vergelijkt deze met een **persoonsgroep**. Als het een gedetecteerd gezicht kan overeenkomen met een **persoon,** slaat het het resultaat op. Met deze code worden gedetailleerde wedstrijdresultaten afgedrukt op de console.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify)]

## <a name="verify-faces"></a>Gezichten verifiëren

Met de bewerking Verifiëren wordt een face-id en een ander face-id of een **persoonsobject** gebruikt en wordt bepaald of deze van dezelfde persoon zijn.

De volgende code detecteert gezichten in twee bronafbeeldingen en verifieert deze vervolgens tegen een gezicht dat is gedetecteerd vanuit een doelafbeelding.

### <a name="get-test-images"></a>Testafbeeldingen bekijken

De volgende codeblokken declareren variabelen die naar de bron- en doelafbeeldingen voor de verificatiebewerking wijzen.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_baseurl)]

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_photos)]

### <a name="detect-faces-for-verification"></a>Gezichten detecteren voor verificatie

De volgende code detecteert gezichten in de bron en doelafbeeldingen en slaat deze op variabelen op.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_detect)]

### <a name="get-verification-results"></a>Verificatieresultaten behalen

Met de volgende code worden elk van de bronafbeeldingen vergeleken met de doelafbeelding en wordt een bericht afgedrukt dat aangeeft of ze van dezelfde persoon zijn.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify)]

## <a name="take-a-snapshot-for-data-migration"></a>Een momentopname maken voor gegevensmigratie

Met de functie Momentopnamen u uw opgeslagen gezichtsgegevens, zoals een getrainde **persoonsgroep,** verplaatsen naar een ander Azure Cognitive Services Face-abonnement. U deze functie gebruiken als u bijvoorbeeld een **PersonGroup-object** hebt gemaakt met een gratis proefabonnement en het nu wilt migreren naar een betaald abonnement. Zie de [gegevens van Uw gezicht migreren](../Face-API-How-to-Topics/how-to-migrate-face-data.md) voor een breed overzicht van de functie Momentopnamen.

In dit voorbeeld migreert u de **persoonsgroep die** u hebt gemaakt in [Een personengroep maken en trainen.](#create-and-train-a-person-group) U die sectie eerst voltooien of uw eigen Face-gegevensconstructie(s) gebruiken.

### <a name="set-up-target-subscription"></a>Doelabonnement instellen

Ten eerste moet u een tweede Azure-abonnement hebben met een Face-bron. u dit doen door de stappen in de sectie [Instellen te](#setting-up) volgen. 

Maak vervolgens de volgende variabelen boven aan het script. U moet ook nieuwe omgevingsvariabelen maken voor de abonnements-id van uw Azure-account, evenals de sleutel-, eindpunt- en abonnements-id van uw nieuwe (doel)account. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshotvars)]

### <a name="authenticate-target-client"></a>Doelclient verifiëren

Sla later in uw script uw huidige clientobject op als bronclient en verifieer vervolgens een nieuw clientobject voor uw doelabonnement. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_auth)]

### <a name="use-a-snapshot"></a>Een momentopname gebruiken

De rest van de momentopnamebewerkingen vindt plaats binnen een asynchrone functie. 

1. De eerste stap is om de momentopname te **maken,** die de gezichtsgegevens van uw oorspronkelijke abonnement opslaat op een tijdelijke cloudlocatie. Met deze methode wordt een id geretourneerd die u gebruikt om de status van de bewerking op te vragen.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_take)]

1. Vraag vervolgens de id op totdat de bewerking is voltooid.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait)]

    Deze code maakt `wait_for_operation` gebruik van de functie, die u afzonderlijk moet definiëren:

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_waitforop)]

1. Ga terug naar je asynchrone functie. Gebruik de **toepassingsbewerking** om uw gezichtsgegevens naar uw doelabonnement te schrijven. Met deze methode wordt ook een ID geretourneerd.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_apply)]

1. Gebruik de `wait_for_operation` functie nogmaals om de id op te vragen totdat de bewerking is voltooid.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait2)]

Zodra u deze stappen hebt voltooid, hebt u toegang tot de verzamelingen van gezichtsgegevens van uw nieuwe (doel)abonnement.

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing `python` uit met de opdracht voor uw quickstartbestand.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een abonnement voor Cognitive Services wilt opschonen en verwijderen, u de bron- of brongroep verwijderen. Als u de brongroep verwijdert, worden ook andere bronnen verwijderd die eraan zijn gekoppeld.

* [Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure-CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)

Als u in deze quickstart een **persoonsgroep** hebt gemaakt en deze wilt verwijderen, voert u de volgende code in uw script uit:

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

Als u gegevens hebt gemigreerd met de functie Momentopname in deze quickstart, moet u ook de **persoonsgroep** verwijderen die is opgeslagen in het doelabonnement.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletetargetgroup)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart heb je geleerd hoe je de Face-bibliotheek voor Python gebruiken om basistaken uit te voeren. Bekijk vervolgens de referentiedocumentatie voor meer informatie over de bibliotheek.

> [!div class="nextstepaction"]
> [Verwijzing naar Face API (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)

* [Wat is de Face-service?](../overview.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py)
