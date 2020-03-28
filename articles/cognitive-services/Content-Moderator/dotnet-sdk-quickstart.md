---
title: 'Snelstart: clientbibliotheek voor inhoudsmoderator voor .NET'
titleSuffix: Azure Cognitive Services
description: Ga aan de slag met de contentmoderator-clientbibliotheek voor .NET met deze quickstart.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: dccd2ebb6ac2c11e19e986d39eabda5f0ab6a8fc
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774291"
---
# <a name="quickstart-content-moderator-client-library-for-net"></a>Snelstart: clientbibliotheek voor inhoudsmoderator voor .NET

Ga aan de slag met de contentmoderatorclientbibliotheek voor .NET. Volg deze stappen om het pakket te installeren en probeer de voorbeeldcode voor basistaken uit. Content Moderator is een cognitieve service die tekst-, beeld- en video-inhoud controleert op materiaal dat mogelijk aanstootgevend, riskant of anderszins ongewenst is. Wanneer dergelijk materiaal wordt gevonden, past de service de relevante labels (vlaggen) op de inhoud toe. Uw app kan vervolgens gemarkeerde inhoud verwerken om te voldoen aan de regelgeving of de beoogde omgeving voor gebruikers te onderhouden.

Gebruik de clientbibliotheek Inhoudsmoderator voor .NET om:

* [Matige tekst](#moderate-text)
* [Matige afbeeldingen](#moderate-images)
* [Een beoordeling maken](#create-a-review)

[Voorbeeld van naslagdocumentatie](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-dotnet) | [Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator) | [(NuGet)-voorbeelden](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) | [Samples](https://docs.microsoft.com/azure/cognitive-services/content-moderator/samples-dotnet)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Instellen

### <a name="create-a-content-moderator-azure-resource"></a>Een Azure-bron voor inhoudsmoderator maken

Azure Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een bron voor Inhoudsmoderator met behulp van de [Azure-portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine. U kunt ook het volgende doen:

* Ontvang een [proefsleutel](https://azure.microsoft.com/try/cognitive-services/#decision) die zeven dagen gratis geldig is. Nadat u zich hebt aangemeld, is deze beschikbaar op de [Azure-website.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Uw bron weergeven op de [Azure-portal](https://portal.azure.com/)

Nadat u een sleutel uit uw proefabonnement of resource hebt opgehaald, maakt `CONTENT_MODERATOR_SUBSCRIPTION_KEY` u `CONTENT_MODERATOR_ENDPOINT` [omgevingsvariabelen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor respectievelijk de naam van de sleutel- en eindpunt-URL.

### <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

Maak een nieuwe .NET Core-toepassing in de gewenste teksteditor of IDE. 

Gebruik de `dotnet new` opdracht om in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe console-app met de naam `content-moderator-quickstart`te maken. Met deze opdracht wordt een eenvoudig "Hello World" C#-project gemaakt met één bronbestand: *Program.cs*.

```console
dotnet new console -n content-moderator-quickstart
```

Wijzig uw map in de nieuw gemaakte app-map. U de toepassing bouwen met:

```console
dotnet build
```

De buildoutput mag geen waarschuwingen of fouten bevatten. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

Open in de projectmap het *Program.cs* bestand in uw voorkeurseditor of IDE. Voeg de volgende `using` instructies toe:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_using)]

Maak in de klasse **Programma** variabelen voor de eindpuntlocatie en sleutel van uw resource als omgevingsvariabelen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_creds)]

> [!NOTE]
> Als u de omgevingsvariabelen hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell sluiten en opnieuw openen om toegang te krijgen tot de variabelen.

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Installeer in de toepassingsmap de clientbibliotheek Inhoudsmoderator voor .NET met de volgende opdracht:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

Als u de Visual Studio IDE gebruikt, is de clientbibliotheek beschikbaar als downloadbaar NuGet-pakket.

## <a name="object-model"></a>Objectmodel

De volgende klassen behandelen enkele van de belangrijkste functies van de Content Moderator .NET SDK.

|Name|Beschrijving|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient?view=azure-dotnet)|Deze klasse is nodig voor alle functionaliteit van Content Moderator. U wilt deze met uw abonnementsgegevens en u gebruikt deze om exemplaren van andere klassen te produceren.|
|[ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet)|Deze klasse biedt de functionaliteit voor het analyseren van afbeeldingen voor inhoud voor volwassenen, persoonlijke informatie of menselijke gezichten.|
|[TextModeration TextModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation?view=azure-dotnet)|Deze klasse biedt de functionaliteit voor het analyseren van tekst voor taal, godslastering, fouten en persoonlijke informatie.|
|[Beoordelingen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet)|Deze klasse biedt de functionaliteit van de Beoordelingens-API's, inclusief de methoden voor het maken van taken, aangepaste werkstromen en menselijke beoordelingen.|

## <a name="code-examples"></a>Codevoorbeelden


In deze codefragmenten ziet u hoe u de volgende taken uitvoeren met de clientbibliotheek Inhoudsmoderator voor .NET:

* [De client verifiëren](#authenticate-the-client)
* [Matige tekst](#moderate-text)
* [Matige afbeeldingen](#moderate-images)
* [Een beoordeling maken](#create-a-review)

## <a name="authenticate-the-client"></a>De client verifiëren

In een nieuwe methode u clientobjecten instantiëren met uw eindpunt en sleutel. U hebt niet voor elk scenario een andere client nodig, maar het kan wel helpen om uw code georganiseerd te houden.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_client)]

## <a name="moderate-text"></a>Matige tekst

De volgende code gebruikt een Content Moderator-client om een teksttekst te analyseren en de resultaten op de console af te drukken. Definieer in de hoofdmap van de klasse **Programma** invoer- en uitvoerbestanden:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_text_vars)]

Voeg vervolgens aan de basis van uw project een *TextFile.txt-bestand* toe. Voeg uw eigen tekst toe aan dit bestand of gebruik de volgende voorbeeldtekst:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```

Voeg de volgende methodeaanroepen toe aan uw `Main` methode:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_textmod_call)]

Definieer vervolgens de methode voor tekstmatiging ergens in de klasse **Programma:**

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>Matige afbeeldingen

De volgende code maakt gebruik van een Content Moderator client, samen met een [ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet) object, om externe afbeeldingen te analyseren voor volwassen en pikante inhoud.

> [!NOTE]
> U ook de inhoud van een lokale afbeelding analyseren. Zie de [referentiedocumentatie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) voor methoden en bewerkingen die werken met lokale afbeeldingen.

### <a name="get-sample-images"></a>Voorbeeldafbeeldingen bekijken

Definieer uw invoer- en uitvoerbestanden:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_image_vars)]

Maak vervolgens het invoerbestand *ImageFiles.txt*aan de basis van uw project. In dit bestand voegt u de URL's van afbeeldingen toe om op elke regel één URL te analyseren.&mdash; U de volgende voorbeeldafbeeldingen gebruiken:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

Geef uw invoer- en uitvoerbestanden door `Main` naar de volgende methodeaanroep in de methode. U definieert deze methode in een latere stap.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_textmod_call)]

### <a name="define-helper-class"></a>Helperklasse definiëren

Voeg de volgende klassendefinitie toe in de klasse **Programma.** Deze binnenklasse verwerkt de resultaten van beeldmatiging.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>De methode voor beeldbeheer definiëren

Met de volgende methode worden de URL's van de afbeelding in een tekstbestand gebruikt, wordt een **instantie EvaluationData** gemaakt en wordt de afbeelding geanalyseerd voor inhoud voor volwassenen/racy, tekst en menselijke gezichten. Vervolgens wordt de uiteindelijke **instantie EvaluationData** aan een lijst toegevoegd en wordt de volledige lijst met geretourneerde gegevens naar de console geschreven.

#### <a name="iterate-through-image-urls"></a>Herhalen door beeld-URL's

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>Inhoud analyseren

Zie de handleiding [Voor](./image-moderation-api.md) afbeeldingsconcepten voor meer informatie over de afbeeldingskenmerken waarvoor inhoudsmoderator wordt weergegeven.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>Moderatieresultaten schrijven om in te dienen

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>Een beoordeling maken

U de Inhoudsmoderator .NET SDK gebruiken om inhoud in de [beoordelingstool](https://contentmoderator.cognitive.microsoft.com) te integreren, zodat menselijke moderators deze kunnen bekijken. Zie de conceptgids Voor het controleren van [de tool.](./review-tool-user-guide/human-in-the-loop.md)

De methode in deze sectie gebruikt de klasse [Reviews](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet) om een beoordeling te maken, de id op te halen en de gegevens ervan te controleren nadat ze menselijke invoer hebben ontvangen via de webportal van de beoordelingstool. Het registreert al deze informatie in een uitvoertekstbestand. Roep de methode `Main` aan vanuit uw methode:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_call)]

### <a name="get-sample-images"></a>Voorbeeldafbeeldingen bekijken

Declareer de volgende array aan de basis van uw **programmaklasse.** Deze variabele verwijst naar een voorbeeldafbeelding die u wilt gebruiken om de revisie te maken.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>Controlereferenties ophalen

Meld u aan bij het [gereedschap Controleren](https://contentmoderator.cognitive.microsoft.com) en haal uw teamnaam op. Wijs het vervolgens toe aan de juiste variabele in de klasse **Programma.** Optioneel u een callback-eindpunt instellen om updates te ontvangen over de activiteit van de beoordeling.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>Helperklasse definiëren

Voeg de volgende klassendefinitie toe in de klasse **Programma.** Deze klasse wordt gebruikt om één revisieinstantie weer te geven die wordt ingediend bij het hulpprogramma Controleren.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>Helpermethode definiëren

Voeg de volgende methode toe aan de klasse **Programma.** Met deze methode worden de resultaten van controlequery's naar het uitvoertekstbestand geschreven.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>De methode voor het maken van revisie definiëren

Nu bent u klaar om de methode te definiëren waarmee de revisiecreatie en query's worden verwerkt. Voeg een nieuwe methode **toe, CreateReviews**en definieer de volgende lokale variabelen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>Beoordelingen plaatsen op de tool Beoordeling

Voeg vervolgens de volgende code toe om de gegeven voorbeeldafbeeldingen te herhalen, metagegevens toe te voegen en deze in één batch naar het gereedschap Controleren te verzenden. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_create)]

Het object dat wordt geretourneerd uit de API-aanroep bevat unieke ID-waarden voor elke geüploade afbeelding. De volgende code onteert deze id's en gebruikt deze vervolgens om Inhoudsmoderator op te vragen voor de status van elke afbeelding in de batch.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>Lees meer details van de beoordeling

De volgende code zorgt ervoor dat het programma wacht op invoer van de gebruiker. Wanneer u bij deze stap bij runtime aankomt, u zelf naar de [tool Controleren](https://contentmoderator.cognitive.microsoft.com) gaan, controleren of de voorbeeldafbeelding is geüpload en ermee communiceren. Zie de [handleiding Beoordelingen voor](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images)informatie over hoe u met een beoordeling communiceren. Wanneer u klaar bent, u op elke toets drukken om het programma voort te zetten en de resultaten van het beoordelingsproces op te halen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_results)]

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

Voer de toepassing uit uw `dotnet run` toepassingsmap uit met de opdracht.

```dotnet
dotnet run 
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een abonnement voor Cognitive Services wilt opschonen en verwijderen, u de bron- of brongroep verwijderen. Als u de brongroep verwijdert, worden ook andere bronnen verwijderd die eraan zijn gekoppeld.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart heb je geleerd hoe je de Bibliotheek Inhoudsmoderator .NET gebruiken om moderatietaken uit te voeren. Leer vervolgens meer over de moderatie van afbeeldingen of andere media door een conceptuele handleiding te lezen.

> [!div class="nextstepaction"]
> [Beeldmoderatieconcepten](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Wat is Azure Content Moderator?](./overview.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub.](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ContentModerator/Program.cs)
