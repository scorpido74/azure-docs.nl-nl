---
title: 'Snelstartgids: Content Moderator-client bibliotheek voor .NET | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Ga aan de slag met de Content Moderator-client bibliotheek voor .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 10/25/2019
ms.author: pafarley
ms.openlocfilehash: cb812a0432e5fdb2828054751ef35f3de82226e7
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539034"
---
# <a name="quickstart-content-moderator-client-library-for-net"></a>Snelstartgids: Content Moderator-client bibliotheek voor .NET

Ga aan de slag met de Content Moderator-client bibliotheek voor .NET. Volg deze stappen om het pakket te installeren en de voorbeeld code voor basis taken uit te proberen. Content Moderator is een cognitieve service waarmee tekst-, afbeeldings-en video-inhoud wordt gecontroleerd op materiaal dat mogelijk aanstootgevend, riskant of anderszins ongewenst is. Wanneer dergelijk materiaal wordt gevonden, past de service de relevante labels (vlaggen) op de inhoud toe. Uw app kan vervolgens gemarkeerde inhoud verwerken om te voldoen aan de voor Schriften of de beoogde omgeving voor gebruikers te onderhouden.

Gebruik de Content Moderator-client bibliotheek voor .NET voor het volgende:

* [Gemiddelde tekst](#moderate-text)
* [Gemiddelde afbeeldingen](#moderate-images)
* [Een beoordeling maken](#create-a-review)

[Referentie documentatie](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-dotnet) | - [bibliotheek bron code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator) | [pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) | -voor [beelden](https://docs.microsoft.com/azure/cognitive-services/content-moderator/samples-dotnet)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [.net core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Instellen

### <a name="create-a-content-moderator-azure-resource"></a>Een Content Moderator Azure-resource maken

Azure-Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een resource voor Content Moderator met behulp van de [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure cli](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine. U kunt ook het volgende doen:

* Ontvang een [proef sleutel](https://azure.microsoft.com/try/cognitive-services/#decision) die zeven dagen gratis geldig is. Nadat u zich hebt aangemeld, is deze beschikbaar op de [Azure-website](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Bekijk uw resource op het [Azure Portal](https://portal.azure.com/)

Nadat u een sleutel van uw proef abonnement of resource hebt ontvangen, moet u [omgevings variabelen maken](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de sleutel en eind punt-URL, respectievelijk met de naam `CONTENT_MODERATOR_SUBSCRIPTION_KEY` en `CONTENT_MODERATOR_ENDPOINT`.

### <a name="create-a-new-c-application"></a>Een nieuwe C# toepassing maken

Maak een nieuwe .NET core-toepassing in uw favoriete tekst editor of IDE. 

Gebruik in een console venster (zoals cmd, Power shell of bash) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam `content-moderator-quickstart`. Met deze opdracht maakt u een eenvoudig ' C# Hallo wereld '-project met één bron bestand: *Program.cs*.

```console
dotnet new console -n content-moderator-quickstart
```

Wijzig uw directory in de zojuist gemaakte app-map. U kunt de toepassing samen stellen met:

```console
dotnet build
```

De build-uitvoer mag geen waarschuwingen of fouten bevatten. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

Open het *Program.cs* -bestand in de map van het project in uw voorkeurs editor of IDE. Voeg de volgende `using` instructies toe:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_using)]

Maak in de klasse **programma** variabelen voor de eindpunt locatie en-sleutel van uw resource als omgevings variabelen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_creds)]

> [!NOTE]
> Als u de omgevings variabelen hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell waarmee deze wordt uitgevoerd, sluiten en opnieuw openen om toegang te krijgen tot de variabelen.

### <a name="install-the-client-library"></a>De client bibliotheek installeren

Installeer de Content Moderator-client bibliotheek voor .NET in de toepassingsmap met de volgende opdracht:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

Als u de Visual Studio IDE gebruikt, is de client bibliotheek beschikbaar als een downloadbaar NuGet-pakket.

## <a name="object-model"></a>Object model

De volgende klassen behandelen enkele van de belangrijkste functies van de Content Moderator .NET SDK.

|Naam|Beschrijving|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient?view=azure-dotnet)|Deze klasse is nodig voor alle Content Moderator functionaliteit. U maakt de app met uw abonnements gegevens en gebruikt deze om instanties van andere klassen te maken.|
|[ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet)|Deze klasse biedt de functionaliteit voor het analyseren van installatie kopieën voor inhoud voor volwassenen, persoonlijke gegevens of menselijke gezichten.|
|[TextModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation?view=azure-dotnet)|Deze klasse biedt de functionaliteit voor het analyseren van tekst voor taal-, Grove-, fout-en persoonlijke gegevens.|
|[Evaluatie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet)|Deze klasse biedt de functionaliteit van de controle-Api's, met inbegrip van de methoden voor het maken van taken, aangepaste werk stromen en mensen Beoordelingen.|

## <a name="code-examples"></a>Code voorbeelden


Deze code fragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Content Moderator-client bibliotheek voor .NET:

* [De client verifiëren](#authenticate-the-client)
* [Gemiddelde tekst](#moderate-text)
* [Gemiddelde afbeeldingen](#moderate-images)
* [Een beoordeling maken](#create-a-review)

## <a name="authenticate-the-client"></a>De client verifiëren

In een nieuwe methode maakt u client objecten met het eind punt en de sleutel. U hebt voor elk scenario geen andere client nodig, maar het kan helpen uw code te organiseren.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_client)]

## <a name="moderate-text"></a>Gemiddelde tekst

De volgende code gebruikt een Content Moderator-client om een tekst hoofdtekst te analyseren en de resultaten in de-console af te drukken. In de hoofdmap van uw **programma** klasse definieert u de invoer-en uitvoer bestanden:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_text_vars)]

Klik vervolgens in de hoofdmap van het project en voeg een *TextFile. txt* -bestand toe. Voeg uw eigen tekst toe aan dit bestand of gebruik de volgende voorbeeld tekst:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```

Voeg de volgende methode aanroep toe aan de `Main` methode:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_textmod_call)]

Vervolgens definieert u een methode voor tekst toezicht ergens in uw **programma** klasse:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>Gemiddelde afbeeldingen

De volgende code gebruikt een Content Moderator-client, samen met een [ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet) -object, voor het analyseren van externe installatie kopieën voor inhoud voor volwassenen en ongepaste.

> [!NOTE]
> U kunt ook de inhoud van een lokale installatie kopie analyseren. Zie de [referentie documentatie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) voor methoden en bewerkingen die met lokale installatie kopieën werken.

### <a name="get-sample-images"></a>Voorbeeld afbeeldingen ophalen

Definieer de invoer-en uitvoer bestanden:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_image_vars)]

Maak vervolgens het invoer bestand *ImageFiles. txt*in de hoofdmap van het project. In dit bestand voegt u de Url's van installatie kopieën toe om&mdash;één URL op elke regel te analyseren. U kunt de volgende voorbeeld afbeeldingen gebruiken:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

Geef de invoer-en uitvoer bestanden op in de volgende methode aanroep in de `Main` methode. U definieert deze methode in een latere stap.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_textmod_call)]

### <a name="define-helper-class"></a>Helper-klasse definiëren

Voeg de volgende klassen definitie toe aan de klasse **Program** . Met deze binnenste klasse worden de resultaten van de afbeeldings controle afgehandeld.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>De methode voor afbeeldings toezicht definiëren

Met de volgende methode worden de afbeeldings-Url's in een tekst bestand door lopen, wordt een **EvaluationData** -exemplaar gemaakt en wordt de installatie kopie geanalyseerd voor volwassenen/ongepaste-inhoud, tekst en menselijke gezichten. Vervolgens wordt het laatste **EvaluationData** -exemplaar aan een lijst toegevoegd en wordt de volledige lijst met geretourneerde gegevens naar de console geschreven.

#### <a name="iterate-through-image-urls"></a>De afbeeldings-Url's door lopen

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>Inhoud analyseren

Zie de hand leiding voor de installatie van de [afbeeldings toezicht](./image-moderation-api.md) voor meer informatie over de afbeeldings kenmerken die content moderator schermen voor.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>Resultaten van toezicht naar bestand schrijven

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>Een beoordeling maken

U kunt de Content Moderator .NET SDK gebruiken om inhoud in te voeren in het [beoordelings programma](https://contentmoderator.cognitive.microsoft.com) zodat menselijke moderators deze kunnen beoordelen. Zie de [conceptuele hand leiding voor het beoordelings programma](./review-tool-user-guide/human-in-the-loop.md)voor meer informatie over het hulp programma.

Voor de methode in deze sectie wordt gebruikgemaakt van de [beoordelingen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet) klasse voor het maken van een beoordeling, het ophalen van de id en het controleren van de details na het ontvangen van menselijke invoer via de webportal van het controle programma. Al deze gegevens worden geregistreerd in een tekst bestand met uitvoer. Roep de methode aan vanuit de `Main` methode:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_call)]

### <a name="get-sample-images"></a>Voorbeeld afbeeldingen ophalen

Declareer de volgende matrix in de hoofdmap van de **programma** klasse. Deze variabele verwijst naar een voorbeeld afbeelding die moet worden gebruikt om de beoordeling te maken.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>Controle referenties ophalen

Meld u aan bij het [beoordelings programma](https://contentmoderator.cognitive.microsoft.com) en haal uw team naam op. Wijs deze vervolgens toe aan de juiste variabele in de klasse **Program** . U kunt desgewenst een call back-eind punt instellen om updates te ontvangen over de activiteit van de beoordeling.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>Helper-klasse definiëren

Voeg de volgende klassen definitie toe binnen de **programma** klasse. Deze klasse wordt gebruikt om een enkel controle-exemplaar aan te duiden dat wordt verzonden naar het hulp programma beoordeling.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>Hulp methode definiëren

Voeg de volgende methode toe aan de klasse **Program**. Met deze methode worden de resultaten van controle query's naar het uitvoer tekst bestand geschreven.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>Definieer de methode voor het maken van de beoordeling

Nu bent u klaar om de methode te definiëren waarmee de revisie wordt gemaakt en query's worden uitgevoerd. Voeg een nieuwe methode toe, **CreateReviews**en definieer de volgende lokale variabelen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>Beoordelingen plaatsen bij het beoordelings programma

Voeg vervolgens de volgende code toe om de opgegeven voorbeeld afbeeldingen door te lopen, meta gegevens toe te voegen en deze te verzenden naar het hulp programma beoordeling in één batch. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_create)]

Het object dat wordt geretourneerd door de API-aanroep bevat unieke ID-waarden voor elke geüploade afbeelding. Met de volgende code worden deze Id's geparseerd, waarna deze worden gebruikt om Content Moderator te zoeken naar de status van elke installatie kopie in de batch.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>Details van controle ophalen

De volgende code zorgt ervoor dat het programma wacht op invoer van de gebruiker. Wanneer u tijdens runtime aan deze stap komt, gaat u naar het [controle programma](https://contentmoderator.cognitive.microsoft.com) zelf, controleert u of de voorbeeld afbeelding is geüpload en communiceert deze. Voor informatie over het gebruik van een beoordeling raadpleegt u de [hand leiding voor beoordelingen](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images). Wanneer u klaar bent, kunt u op een wille keurige toets drukken om door te gaan met het programma en de resultaten van het controle proces ophalen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_results)]

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

Voer de toepassing uit vanuit de toepassingsmap met de opdracht `dotnet run`.

```dotnet
dotnet run 
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen


In deze Quick Start hebt u geleerd hoe u de Content Moderator .NET-bibliotheek kunt gebruiken om beheer taken uit te voeren. Lees vervolgens een conceptuele hand leiding voor meer informatie over de toezicht op installatie kopieën of andere media.

> [!div class="nextstepaction"]
> [Concepten van afbeeldings toezicht](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Wat is Azure Content Moderator?](./overview.md)
* De broncode voor dit voorbeeld is te vinden [op GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ContentModerator/Program.cs).
