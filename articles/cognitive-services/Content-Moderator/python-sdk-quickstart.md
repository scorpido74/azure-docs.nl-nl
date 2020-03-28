---
title: 'Snelstart: contentmoderator-clientbibliotheek voor Python'
titleSuffix: Azure Cognitive Services
description: In deze quickstart leert u hoe u aan de slag met de azure Cognitive Services Content Moderator-clientbibliotheek voor Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 31cdc9663283b580acc10e7ac8d5a77f9036a7a8
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772361"
---
# <a name="quickstart-content-moderator-client-library-for-python"></a>Snelstart: contentmoderator-clientbibliotheek voor Python

Ga aan de slag met de contentmoderator-clientbibliotheek voor Python. Volg deze stappen om het pakket te installeren en probeer de voorbeeldcode voor basistaken uit. Content Moderator is een cognitieve service die tekst-, beeld- en video-inhoud controleert op materiaal dat mogelijk aanstootgevend, riskant of anderszins ongewenst is. Wanneer dergelijk materiaal wordt gevonden, past de service de relevante labels (vlaggen) op de inhoud toe. Uw app kan gelabelde inhoud vervolgens afhandelen om te voldoen aan de regelgeving of om een beoogde omgeving voor gebruikers te beheren.

Gebruik de clientbibliotheek Inhoudsmoderator voor Python om:

* [Matige tekst](#moderate-text)
* [Een aangepaste lijst met termen gebruiken](#use-a-custom-terms-list)
* [Matige afbeeldingen](#moderate-images)
* [Een aangepaste afbeeldingslijst gebruiken](#use-a-custom-image-list)
* [Een beoordeling maken](#create-a-review)

[Voorbeeld van broncode](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/contentmoderator?view=azure-python) |  | van[naslagdocumentatieBibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-contentmoderator)[(PiPy)-voorbeelden](https://pypi.org/project/azure-cognitiveservices-vision-contentmoderator/) | [Samples](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Instellen

### <a name="create-a-content-moderator-azure-resource"></a>Een Azure-bron voor inhoudsmoderator maken

Azure Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een bron voor Inhoudsmoderator met behulp van de [Azure-portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine. U kunt ook het volgende doen:

* Ontvang een [proefsleutel](https://azure.microsoft.com/try/cognitive-services/#decision) die zeven dagen gratis geldig is. Nadat u zich hebt aangemeld, is deze beschikbaar op de [Azure-website.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Uw bron weergeven op de [Azure-portal](https://portal.azure.com/)

Nadat u een sleutel uit uw proefabonnement of resource hebt opgehaald, maakt `CONTENT_MODERATOR_SUBSCRIPTION_KEY` u `CONTENT_MODERATOR_ENDPOINT` [omgevingsvariabelen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor respectievelijk de naam van de sleutel- en eindpunt-URL.
 
### <a name="create-a-python-script"></a>Een python-script maken

Maak een nieuw Python-script en open het in uw voorkeurseditor of IDE. Voeg vervolgens `import` de volgende instructies toe aan de bovenkant van het bestand.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imports)]

Maak vervolgens variabelen voor de eindpuntlocatie en sleutel van uw resource als omgevingsvariabelen. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_vars)]

> [!NOTE]
> Als u de omgevingsvariabelen hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell sluiten en opnieuw openen om toegang te krijgen tot de variabelen.

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

U de clientbibliotheek Inhoudsmoderator installeren met de volgende opdracht:

```console
pip install --upgrade azure-cognitiveservices-vision-contentmoderator
```

## <a name="object-model"></a>Objectmodel

De volgende klassen behandelen enkele van de belangrijkste functies van de Content Moderator Python SDK.

|Name|Beschrijving|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python)|Deze klasse is nodig voor alle functionaliteit van Content Moderator. U wilt deze met uw abonnementsgegevens en u gebruikt deze om exemplaren van andere klassen te produceren.|
|[ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python)|Deze klasse biedt de functionaliteit voor het analyseren van afbeeldingen voor inhoud voor volwassenen, persoonlijke informatie of menselijke gezichten.|
|[TextModerationOperations TextModerationOperations TextModerationOperations TextModerati](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)|Deze klasse biedt de functionaliteit voor het analyseren van tekst voor taal, godslastering, fouten en persoonlijke informatie.|
[BeoordelingenOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python)|Deze klasse biedt de functionaliteit van de Beoordelingens-API's, inclusief de methoden voor het maken van taken, aangepaste werkstromen en menselijke beoordelingen.|

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken uitvoeren met de clientbibliotheek voor inhoudsmoderator voor Python:

* [De client verifiëren](#authenticate-the-client)
* [Matige tekst](#moderate-text)
* [Een aangepaste lijst met termen gebruiken](#use-a-custom-terms-list)
* [Matige afbeeldingen](#moderate-images)
* [Een aangepaste afbeeldingslijst gebruiken](#use-a-custom-image-list)
* [Een beoordeling maken](#create-a-review)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> In deze quickstart wordt ervan uitgegaan dat u [omgevingsvariabelen](../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) hebt gemaakt voor de sleutel en eindpunt van inhoudsmoderator.

Instantiate een klant met uw eindpunt en sleutel. Maak een [Object CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) met uw sleutel en gebruik het met uw eindpunt om een [ContentModeratorClient-object](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python) te maken.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_client)]

## <a name="moderate-text"></a>Matige tekst

De volgende code gebruikt een Content Moderator-client om een teksttekst te analyseren en de resultaten op de console af te drukken. Maak eerst een **text_files/map** aan de basis van uw project en voeg een *bestand van content_moderator_text_moderation.txt* toe. Voeg uw eigen tekst toe aan dit bestand of gebruik de volgende voorbeeldtekst:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 2065550111
```

Voeg een verwijzing toe aan de nieuwe map.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

Voeg vervolgens de volgende code toe aan uw Python-script.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textmod)]

## <a name="use-a-custom-terms-list"></a>Een aangepaste lijst met termen gebruiken

In de volgende code ziet u hoe u een lijst met aangepaste termen voor tekstbeheer beheert. U de klasse [ListManagementTermListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementtermlistsoperations?view=azure-python) gebruiken om een lijst met termen te maken, de afzonderlijke voorwaarden te beheren en andere tekstlichamen ertegen te screenen.

### <a name="get-sample-text"></a>Voorbeeldtekst opmaken

Als u dit voorbeeld wilt gebruiken, moet u een **text_files/map** aan de hoofdmap van uw project maken en een *content_moderator_term_list.txt-bestand* toevoegen. Dit bestand moet organische tekst bevatten die wordt gecontroleerd aan de hand van de lijst met termen. U de volgende voorbeeldtekst gebruiken:

```
This text contains the terms "term1" and "term2".
```

Voeg een verwijzing toe aan de map als u er nog geen hebt gedefinieerd.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

### <a name="create-a-list"></a>Een lijst maken

Voeg de volgende code toe aan uw Python-script om een aangepaste lijst met termen te maken en de id-waarde op te slaan.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_create)]

### <a name="define-list-details"></a>Lijstgegevens definiëren

U de id van een lijst gebruiken om de naam en beschrijving ervan te bewerken.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_details)]

### <a name="add-a-term-to-the-list"></a>Een term toevoegen aan de lijst

De volgende code `"term1"` voegt `"term2"` de voorwaarden en aan de lijst toe.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_add)]

### <a name="get-all-terms-in-the-list"></a>Alle termen in de lijst opmaken

U de lijst-id gebruiken om alle termen in de lijst terug te sturen.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_getterms)]

### <a name="refresh-the-list-index"></a>De lijstindex vernieuwen

Wanneer u termen toevoegt of uit de lijst verwijdert, moet u de index vernieuwen voordat u de bijgewerkte lijst gebruiken.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_refresh)]

### <a name="screen-text-against-the-list"></a>Tekst op schermen tegen de lijst

De belangrijkste functionaliteit van de lijst met aangepaste termen is om een teksttekst te vergelijken met de lijst en te vinden of er overeenkomende termen zijn. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_screen)]

### <a name="remove-a-term-from-a-list"></a>Een term uit een lijst verwijderen

Met de volgende `"term1"` code wordt de term uit de lijst verwijderd.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_remove)]

### <a name="remove-all-terms-from-a-list"></a>Alle termen uit een lijst verwijderen

Gebruik de volgende code om een lijst met alle voorwaarden te wissen.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_removeall)]

### <a name="delete-list"></a>Lijst verwijderen

Gebruik de volgende code om een aangepaste lijst met termen te verwijderen.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_deletelist)]

## <a name="moderate-images"></a>Matige afbeeldingen

De volgende code maakt gebruik van een Content Moderator client, samen met een [ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python) object, om afbeeldingen te analyseren voor volwassen en pikante inhoud.

### <a name="get-images"></a>Afbeeldingen ophalen

Definieer een verwijzing naar een aantal afbeeldingen die u wilt analyseren.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemodvars)]

Voeg vervolgens de volgende code toe om door uw afbeeldingen te herhalen. De rest van de code in deze sectie zal gaan in deze lus.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Controleer op inhoud voor volwassenen/racy

De volgende code controleert de afbeelding op de opgegeven URL op inhoud voor volwassenen of racy en drukt resultaten af op de console. Zie de handleiding [voor beeldmatigingsconcepten](./image-moderation-api.md) voor informatie over wat deze termen betekenen.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_ar)]

### <a name="check-for-visible-text"></a>Controleren op zichtbare tekst

Met de volgende code wordt de afbeelding gecontroleerd op zichtbare tekstinhoud en worden resultaten afgedrukt op de console.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Controleren op gezichten

De volgende code controleert de afbeelding op menselijke gezichten en drukt resultaten af op de console.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_face)]

## <a name="use-a-custom-image-list"></a>Een aangepaste afbeeldingslijst gebruiken

In de volgende code ziet u hoe u een aangepaste lijst met afbeeldingen beheert voor beeldbeheer. Deze functie is handig als uw platform regelmatig exemplaren ontvangt van dezelfde set afbeeldingen die u wilt screenen. Door een lijst met deze specifieke afbeeldingen bij te houden, u de prestaties verbeteren. Met de klasse [ListManagementImageImagesOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementimagelistsoperations?view=azure-python) u een afbeeldingslijst maken, de afzonderlijke afbeeldingen in de lijst beheren en andere afbeeldingen hiermee vergelijken.

Maak de volgende tekstvariabelen om de afbeeldings-URL's op te slaan die u in dit scenario zult gebruiken.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelistvars)]

> [!NOTE]
> Dit is niet de juiste lijst zelf, maar een informele `add images` lijst van afbeeldingen die zullen worden toegevoegd in het gedeelte van de code.


### <a name="create-an-image-list"></a>Een afbeeldingslijst maken

Voeg de volgende code toe om een afbeeldingslijst te maken en sla een verwijzing naar de id op.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_create)]

### <a name="add-images-to-a-list"></a>Afbeeldingen toevoegen aan een lijst

De volgende code voegt al uw afbeeldingen toe aan de lijst.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_add)]

Definieer de **add_images** helperfunctie elders in uw script.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_addhelper)]

### <a name="get-images-in-list"></a>Afbeeldingen in de lijst opmaken

De volgende code drukt de namen van alle afbeeldingen in uw lijst af.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getimages)]

### <a name="update-list-details"></a>Lijstgegevens bijwerken

U de lijst-id gebruiken om de naam en beschrijving van de lijst bij te werken.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_updatedetails)]

### <a name="get-list-details"></a>Lijstgegevens opvragen

Gebruik de volgende code om de huidige details van uw lijst af te drukken.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getdetails)]

### <a name="refresh-the-list-index"></a>De lijstindex vernieuwen

Nadat u afbeeldingen hebt toegevoegd of verwijderd, moet u de lijstindex vernieuwen voordat u deze gebruiken om andere afbeeldingen weer te geven.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_refresh)]

### <a name="match-images-against-the-list"></a>Afbeeldingen afstemmen op de lijst

De belangrijkste functie van afbeeldingslijsten is om nieuwe afbeeldingen te vergelijken en te zien of er overeenkomsten zijn.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_match)]

### <a name="remove-an-image-from-the-list"></a>Een afbeelding uit de lijst verwijderen

Met de volgende code wordt een item uit de lijst verwijderd. In dit geval is het een afbeelding die niet overeenkomt met de lijstcategorie.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_remove)]

### <a name="remove-all-images-from-a-list"></a>Alle afbeeldingen uit een lijst verwijderen

Gebruik de volgende code om een afbeeldingslijst uit te wissen.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_removeall)]

### <a name="delete-the-image-list"></a>De afbeeldingslijst verwijderen

Gebruik de volgende code om een bepaalde afbeeldingslijst te verwijderen.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_delete)]

## <a name="create-a-review"></a>Een beoordeling maken

U de Python SDK voor inhoudsmoderator gebruiken om inhoud in de [beoordelingstool](https://contentmoderator.cognitive.microsoft.com) te integreren, zodat menselijke moderators deze kunnen bekijken. Zie de conceptgids Voor het controleren van [de tool.](./review-tool-user-guide/human-in-the-loop.md)

De volgende code gebruikt de klasse [ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python) om een beoordeling te maken, de id op te halen en de gegevens ervan te controleren nadat ze menselijke invoer hebben ontvangen via de webportal van de tool Review.

### <a name="get-review-credentials"></a>Referenties bekijken

Meld u eerst aan bij het gereedschap Controleren en haal uw teamnaam op. Wijs het vervolgens toe aan de juiste variabele in de code. Optioneel u een callback-eindpunt instellen om updates te ontvangen over de activiteit van de beoordeling.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_vars)]

### <a name="create-an-image-review"></a>Een afbeeldingsbeoordeling maken

Voeg de volgende code toe om een beoordeling voor de opgegeven afbeeldings-URL te maken en te plaatsen. De code slaat een verwijzing naar de controle-id op. 
[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_create)]

### <a name="get-review-details"></a>Lees meer details van de beoordeling

Gebruik de volgende code om de details van een bepaalde beoordeling te controleren. Nadat u de beoordeling hebt gemaakt, u zelf naar de tool Controleren gaan en communiceren met de inhoud. Voor informatie over hoe dit te doen, zie de [reviews how-to guide](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images). Wanneer u klaar bent, u deze code opnieuw uitvoeren en worden de resultaten van het beoordelingsproces opgehaald.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_getdetails)]

Als u in dit scenario een callback-eindpunt hebt gebruikt, moet het een gebeurtenis in deze indeling ontvangen:

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing `python` uit met de opdracht voor uw quickstartbestand.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een abonnement voor Cognitive Services wilt opschonen en verwijderen, u de bron- of brongroep verwijderen. Als u de brongroep verwijdert, worden ook andere bronnen verwijderd die eraan zijn gekoppeld.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart heb je geleerd hoe je de Python-bibliotheek voor inhoudsmoderator gebruiken om moderatietaken uit te voeren. Leer vervolgens meer over de moderatie van afbeeldingen of andere media door een conceptuele handleiding te lezen.

> [!div class="nextstepaction"]
>[Beeldmoderatieconcepten](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Wat is Azure Content Moderator?](./overview.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py)