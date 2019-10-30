---
title: 'Snelstartgids: Content Moderator-client bibliotheek voor python | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Ga aan de slag met de Content Moderator-client bibliotheek voor python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 10/25/2019
ms.author: pafarley
ms.openlocfilehash: 28c70e7d226e8f4b2458234e1f14754cdbb1825c
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044036"
---
# <a name="quickstart-content-moderator-client-library-for-python"></a>Snelstartgids: Content Moderator-client bibliotheek voor python

Ga aan de slag met de Content Moderator-client bibliotheek voor python. Volg deze stappen om het pakket te installeren en de voorbeeld code voor basis taken uit te proberen. Content Moderator is een cognitieve service waarmee tekst-, afbeeldings-en video-inhoud wordt gecontroleerd op materiaal dat mogelijk aanstootgevend, riskant of anderszins ongewenst is. Wanneer dergelijk materiaal wordt gevonden, past de service de relevante labels (vlaggen) op de inhoud toe. Uw app kan gelabelde inhoud vervolgens afhandelen om te voldoen aan de regelgeving of om een beoogde omgeving voor gebruikers te beheren.

Gebruik de Content Moderator-client bibliotheek voor python voor het volgende:

* [Gemiddelde tekst](#moderate-text)
* [Een lijst met aangepaste voor waarden gebruiken](#use-a-custom-terms-list)
* [Gemiddelde afbeeldingen](#moderate-images)
* [Een aangepaste installatie kopie lijst gebruiken](#use-a-custom-image-list)
* [Een beoordeling maken](#create-a-review)

[Referentie documentatie](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/contentmoderator?view=azure-python) | [bibliotheek bron code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-contentmoderator) | [package (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-contentmoderator/) | -voor[beelden](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Instellen

### <a name="create-a-content-moderator-azure-resource"></a>Een Content Moderator Azure-resource maken

Azure-Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een resource voor Content Moderator met behulp van de [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure cli](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine. U kunt ook het volgende doen:

* Ontvang een [proef sleutel](https://azure.microsoft.com/try/cognitive-services/#decision) die zeven dagen gratis geldig is. Nadat u zich hebt aangemeld, is deze beschikbaar op de [Azure-website](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Bekijk uw resource op het [Azure Portal](https://portal.azure.com/)

Nadat u een sleutel van uw proef abonnement of resource hebt ontvangen, moet u [omgevings variabelen maken](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de sleutel en eind punt-URL, respectievelijk met de naam `CONTENT_MODERATOR_SUBSCRIPTION_KEY` en `CONTENT_MODERATOR_ENDPOINT`.
 
### <a name="create-a-python-script"></a>Een python-script maken

Maak een nieuw python-script en open het in uw voorkeurs editor of IDE. Voeg boven aan het bestand de volgende `import`-instructies toe.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imports)]

Maak vervolgens variabelen voor de eindpunt locatie en-sleutel van uw resource als omgevings variabelen. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_vars)]

> [!NOTE]
> Als u de omgevings variabelen hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell waarmee deze wordt uitgevoerd, sluiten en opnieuw openen om toegang te krijgen tot de variabelen.

### <a name="install-the-client-library"></a>De client bibliotheek installeren

U kunt de Content Moderator-client bibliotheek met de volgende opdracht installeren:

```console
pip install --upgrade azure-cognitiveservices-vision-contentmoderator
```

## <a name="object-model"></a>Object model

De volgende klassen verwerken enkele van de belangrijkste functies van de Content Moderator python SDK.

|Naam|Beschrijving|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python)|Deze klasse is nodig voor alle Content Moderator functionaliteit. U maakt de app met uw abonnements gegevens en gebruikt deze om instanties van andere klassen te maken.|
|[ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python)|Deze klasse biedt de functionaliteit voor het analyseren van installatie kopieën voor inhoud voor volwassenen, persoonlijke gegevens of menselijke gezichten.|
|[TextModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)|Deze klasse biedt de functionaliteit voor het analyseren van tekst voor taal-, Grove-, fout-en persoonlijke gegevens.|
[ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python)|Deze klasse biedt de functionaliteit van de controle-Api's, met inbegrip van de methoden voor het maken van taken, aangepaste werk stromen en mensen Beoordelingen.|

## <a name="code-examples"></a>Code voorbeelden

Deze code fragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Content Moderator-client bibliotheek voor python:

* [De client verifiëren](#authenticate-the-client)
* [Gemiddelde tekst](#moderate-text)
* [Een lijst met aangepaste voor waarden gebruiken](#use-a-custom-terms-list)
* [Gemiddelde afbeeldingen](#moderate-images)
* [Een aangepaste installatie kopie lijst gebruiken](#use-a-custom-image-list)
* [Een beoordeling maken](#create-a-review)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> In deze Quick Start wordt ervan uitgegaan dat u [omgevings variabelen hebt gemaakt](../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) voor de content moderator sleutel en het eind punt.

Exemplaar een client met uw eind punt en sleutel. Maak een [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) -object met uw sleutel en gebruik het met uw eind punt om een [ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python) -object te maken.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_client)]

## <a name="moderate-text"></a>Gemiddelde tekst

De volgende code gebruikt een Content Moderator-client om een tekst hoofdtekst te analyseren en de resultaten in de-console af te drukken. Maak eerst een **text_files/-** map aan de basis van het project en voeg een *content_moderator_text_moderation. txt* -bestand toe. Voeg uw eigen tekst toe aan dit bestand of gebruik de volgende voorbeeld tekst:

```
Is this a grabage email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
Crap is the profanity here. Is this information PII? phone 3144444444
```

Voeg een verwijzing naar de nieuwe map toe.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

Voeg vervolgens de volgende code toe aan het python-script.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textmod)]

## <a name="use-a-custom-terms-list"></a>Een lijst met aangepaste voor waarden gebruiken

De volgende code laat zien hoe u een lijst met aangepaste voor waarden voor tekst toezicht beheert. U kunt de [ListManagementTermListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementtermlistsoperations?view=azure-python) -klasse gebruiken om een lijst met termen te maken, de afzonderlijke voor waarden te beheren en andere tekst teksten ermee te schermen.

### <a name="get-sample-text"></a>Voorbeeld tekst ophalen

Als u dit voor beeld wilt gebruiken, moet u een **text_files/-** map maken in de hoofdmap van het project en een *content_moderator_term_list. txt* -bestand toevoegen. Dit bestand moet een organieke tekst bevatten die wordt gecontroleerd aan de hand van de lijst met voor waarden. U kunt de volgende voorbeeld tekst gebruiken:

```
This text contains the terms "term1" and "term2".
```

Voeg een verwijzing naar de map toe als u deze nog niet hebt gedefinieerd.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

### <a name="create-a-list"></a>Een lijst maken

Voeg de volgende code toe aan het python-script om een lijst met aangepaste voor waarden te maken en de ID-waarde op te slaan.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_create)]

### <a name="define-list-details"></a>Lijst Details definiëren

U kunt de ID van een lijst gebruiken om de naam en beschrijving te bewerken.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_details)]

### <a name="add-a-term-to-the-list"></a>Een term toevoegen aan de lijst

Met de volgende code worden de voor waarden `"term1"` en `"term2"` aan de lijst toegevoegd.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_add)]

### <a name="get-all-terms-in-the-list"></a>Alle voor waarden in de lijst ophalen

U kunt de lijst-ID gebruiken om alle voor waarden in de lijst te retour neren.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_getterms)]

### <a name="refresh-the-list-index"></a>De lijst index vernieuwen

Wanneer u voor waarden in de lijst toevoegt of verwijdert, moet u de index vernieuwen voordat u de bijgewerkte lijst kunt gebruiken.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_refresh)]

### <a name="screen-text-against-the-list"></a>Scherm tekst in de lijst

De belangrijkste functionaliteit van de lijst met aangepaste voor waarden is het vergelijken van een hoofd tekst van de lijst en om te zien of er overeenkomende voor waarden zijn. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_screen)]

### <a name="remove-a-term-from-a-list"></a>Een term uit een lijst verwijderen

Met de volgende code wordt de term `"term1"` verwijderd uit de lijst.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_remove)]

### <a name="remove-all-terms-from-a-list"></a>Alle voor waarden uit een lijst verwijderen

Gebruik de volgende code om een lijst met alle voor waarden te wissen.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_removeall)]

### <a name="delete-list"></a>Lijst verwijderen

Gebruik de volgende code om een lijst met aangepaste voor waarden te verwijderen.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_deletelist)]

## <a name="moderate-images"></a>Gemiddelde afbeeldingen

De volgende code gebruikt een Content Moderator-client, samen met een [ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python) -object, voor het analyseren van afbeeldingen voor inhoud voor volwassenen en ongepaste.

### <a name="get-images"></a>Installatie kopieën ophalen

Definieer een verwijzing naar sommige te analyseren installatie kopieën.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemodvars)]

Voeg vervolgens de volgende code toe om uw afbeeldingen door te laten lopen. De rest van de code in deze sectie gaat in deze lus.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Controleren op inhoud voor volwassenen/ongepaste

Met de volgende code wordt de installatie kopie op de opgegeven URL gecontroleerd op inhoud voor volwassenen of ongepaste en worden de resultaten in de console afgedrukt. Zie de [concepten handleiding voor afbeeldings toezicht](./image-moderation-api.md) voor informatie over de betekenis van deze termen.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_ar)]

### <a name="check-for-visible-text"></a>Controleren op zicht bare tekst

Met de volgende code wordt de afbeelding gecontroleerd op zicht bare tekst inhoud en worden de resultaten weer gegeven in de console.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Controleren op gezichten

Met de volgende code wordt de afbeelding voor menselijke gezichten gecontroleerd en worden de resultaten naar de console afgedrukt.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_face)]

## <a name="use-a-custom-image-list"></a>Een aangepaste installatie kopie lijst gebruiken

De volgende code laat zien hoe u een aangepaste lijst met installatie kopieën voor afbeeldings toezicht beheert. Deze functie is handig als uw platform vaak exemplaren van dezelfde set installatie kopieën ontvangt die u wilt uitschermen. U kunt de prestaties verbeteren door een lijst met deze specifieke installatie kopieën te onderhouden. Met de [ListManagementImageListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementimagelistsoperations?view=azure-python) -klasse kunt u een lijst met installatie kopieën maken, de afzonderlijke installatie kopieën in de lijst beheren en andere afbeeldingen vergelijken.

Maak de volgende tekst variabelen om de afbeeldings-Url's op te slaan die u in dit scenario gebruikt.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelistvars)]

> [!NOTE]
> Dit is niet de juiste lijst zelf, maar een informele lijst met installatie kopieën die worden toegevoegd in de sectie `add images` van de code.


### <a name="create-an-image-list"></a>Een lijst met installatie kopieën maken

Voeg de volgende code toe om een lijst met installatie kopieën te maken en sla een verwijzing op naar de bijbehorende ID.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_create)]

### <a name="add-images-to-a-list"></a>Afbeeldingen toevoegen aan een lijst

Met de volgende code worden al uw installatie kopieën aan de lijst toegevoegd.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_add)]

Definieer de **add_images** -hulp functie elders in het script.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_addhelper)]

### <a name="get-images-in-list"></a>Afbeeldingen in lijst ophalen

Met de volgende code worden de namen van alle afbeeldingen in uw lijst afgedrukt.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getimages)]

### <a name="update-list-details"></a>Details van update lijst

U kunt de lijst-ID gebruiken om de naam en beschrijving van de lijst bij te werken.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_updatedetails)]

### <a name="get-list-details"></a>Details van lijst ophalen

Gebruik de volgende code om de huidige Details van de lijst af te drukken.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getdetails)]

### <a name="refresh-the-list-index"></a>De lijst index vernieuwen

Nadat u installatie kopieën hebt toegevoegd of verwijderd, moet u de lijst index vernieuwen voordat u deze kunt gebruiken om andere installatie kopieën te schermmen.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_refresh)]

### <a name="match-images-against-the-list"></a>Afbeeldingen vergelijken met de lijst

De belangrijkste functie van lijsten met installatie kopieën is om nieuwe afbeeldingen te vergelijken en te controleren of er overeenkomsten zijn.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_match)]

### <a name="remove-an-image-from-the-list"></a>Een installatie kopie verwijderen uit de lijst

Met de volgende code wordt een item uit de lijst verwijderd. In dit geval is het een afbeelding die niet overeenkomt met de categorie lijst.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_remove)]

### <a name="remove-all-images-from-a-list"></a>Alle installatie kopieën verwijderen uit een lijst

Gebruik de volgende code om een lijst met installatie kopieën te wissen.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_removeall)]

### <a name="delete-the-image-list"></a>De lijst met afbeeldingen verwijderen

Gebruik de volgende code om een gegeven lijst met installatie kopieën te verwijderen.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_delete)]

## <a name="create-a-review"></a>Een beoordeling maken

U kunt de Content Moderator python SDK gebruiken om inhoud in te voeren in het [beoordelings programma](https://contentmoderator.cognitive.microsoft.com) , zodat menselijke moderators deze kunnen beoordelen. Zie de [conceptuele hand leiding voor het beoordelings programma](./review-tool-user-guide/human-in-the-loop.md)voor meer informatie over het hulp programma.

De volgende code maakt gebruik van de [ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python) -klasse om een beoordeling te maken, de id op te halen en de details te controleren nadat hij de menselijke invoer heeft ontvangen via de webportal van het controle programma.

### <a name="get-review-credentials"></a>Controle referenties ophalen

Meld u eerst aan bij het beoordelings programma en haal uw team naam op. Wijs deze vervolgens toe aan de juiste variabele in de code. U kunt desgewenst een call back-eind punt instellen om updates te ontvangen over de activiteit van de beoordeling.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_vars)]

### <a name="create-an-image-review"></a>Een afbeeldings beoordeling maken

Voeg de volgende code toe om een beoordeling te maken en te plaatsen voor de opgegeven afbeeldings-URL. Met de code wordt een verwijzing naar de beoordelings-ID opgeslagen. 
[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_create)]

### <a name="get-review-details"></a>Details van controle ophalen

Gebruik de volgende code om de details van een bepaalde beoordeling te controleren. Nadat u de beoordeling hebt gemaakt, kunt u zelf naar het hulp programma controleren gaan en de inhoud gebruiken. Voor informatie over hoe u dit doet, raadpleegt u de [hand leiding voor recensies](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images). Wanneer u klaar bent, kunt u deze code opnieuw uitvoeren en worden de resultaten van het controle proces opgehaald.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_getdetails)]

Als u in dit scenario een call back-eind punt hebt gebruikt, wordt er een gebeurtenis in deze indeling weer gegeven:

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

Voer de toepassing uit met de `python`-opdracht in uw Quick Start-bestand.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u de Content Moderator python-bibliotheek kunt gebruiken om beheer taken uit te voeren. Lees vervolgens een conceptuele hand leiding voor meer informatie over de toezicht op installatie kopieën of andere media.

> [!div class="nextstepaction"]
>[Concepten van afbeeldings toezicht](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Wat is Azure Content Moderator?](./overview.md)
* De broncode voor dit voorbeeld is te vinden [op GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py).