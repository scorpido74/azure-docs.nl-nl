---
title: 'Snelstartgids: Computer Vision-client bibliotheek voor .NET'
description: In deze Snelstartgids gaat u aan de slag met de Computer Vision-client bibliotheek voor .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: aa162fb47eab84ff26af2256f8fe0ba9896b7cf0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448283"
---
# <a name="quickstart-computer-vision-client-library-for-net"></a>Snelstartgids: Computer Vision-client bibliotheek voor .NET

Ga aan de slag met de Computer Vision-client bibliotheek voor .NET. Volg deze stappen om het pakket te installeren en de voorbeeld code voor basis taken uit te proberen. Computer Vision biedt u toegang tot geavanceerde algoritmen voor het verwerken van afbeeldingen en het retour neren van gegevens.

Gebruik de Computer Vision-client bibliotheek voor .NET voor het volgende:

* Analyseer een afbeelding voor Tags, tekst beschrijving, gezichten, inhoud voor volwassenen en meer.
* Gedrukte en handgeschreven tekst herkennen met de batch-API voor lezen.

[Referentie documentatie](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet) | [bibliotheek bron code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision) | [pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [beelden](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [.net core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Instellen

### <a name="create-a-computer-vision-azure-resource"></a>Een Computer Vision Azure-resource maken

Azure-Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een resource voor Computer Vision met behulp van de [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure cli](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine. U kunt ook het volgende doen:

* Ontvang een [proef sleutel](https://azure.microsoft.com/try/cognitive-services/#decision) die zeven dagen gratis geldig is. Nadat u zich hebt aangemeld, is deze beschikbaar op de [Azure-website](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Bekijk uw resource op het [Azure Portal](https://portal.azure.com/).

Nadat u een sleutel van uw proef abonnement of resource hebt ontvangen, moet u [omgevings variabelen maken](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de sleutel en eind punt-URL, respectievelijk met de naam `COMPUTER_VISION_SUBSCRIPTION_KEY` en `COMPUTER_VISION_ENDPOINT`.

### <a name="create-a-new-c-application"></a>Een nieuwe C# toepassing maken

Maak een nieuwe .NET core-toepassing in uw voorkeurs editor of IDE. 

Gebruik in een console venster (zoals cmd, Power shell of bash) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam `computer-vision-quickstart`. Met deze opdracht maakt u een eenvoudig ' C# Hallo wereld '-project met één bron bestand: *Program.cs*.

```console
dotnet new console -n computer-vision-quickstart
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

Open het *Program.cs* -bestand in de map van het project in uw voorkeurs editor of IDE. Voeg de volgende `using`-instructies toe:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_using)]

Maak in de **programma** klasse van de toepassing variabelen voor het Azure-eind punt en de sleutel van uw resource.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_vars)]

### <a name="install-the-client-library"></a>De client bibliotheek installeren

Installeer de Computer Vision-client bibliotheek voor .NET in de toepassingsmap met de volgende opdracht:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 5.0.0
```

Als u de Visual Studio IDE gebruikt, is de client bibliotheek beschikbaar als een downloadbaar NuGet-pakket.

## <a name="object-model"></a>Object model

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Computer Vision .NET SDK.

|Name|Beschrijving|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet) | Deze klasse is nodig voor alle Computer Vision functionaliteit. U maakt de app met uw abonnements gegevens en gebruikt deze om de meeste installatie kopieën uit te voeren.|
|[ComputerVisionClientExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions?view=azure-dotnet)| Deze klasse bevat aanvullende methoden voor de **ComputerVisionClient**.|
|[VisualFeatureTypes](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)| Deze opsomming definieert de verschillende typen afbeeldings analyse die kunnen worden uitgevoerd in een standaard analyse bewerking. U geeft een set VisualFeatureTypes-waarden op, afhankelijk van uw behoeften. |

## <a name="code-examples"></a>Codevoorbeelden

Deze code fragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Computer Vision-client bibliotheek voor .NET:

* [De client verifiëren](#authenticate-the-client)
* [Een afbeelding analyseren](#analyze-an-image)
* [Gedrukte en handgeschreven tekst lezen](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> In deze Quick Start wordt ervan uitgegaan dat u [omgevings variabelen hebt gemaakt](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de computer vision sleutel en het eind punt met de naam `COMPUTER_VISION_SUBSCRIPTION_KEY` en `COMPUTER_VISION_ENDPOINT` respectievelijk.

In een nieuwe methode maakt u een exemplaar van een client met uw eind punt en sleutel. Maak een [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) -object met uw sleutel en gebruik het met uw eind punt om een [ComputerVisionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet) -object te maken.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_auth)]

Waarschijnlijk wilt u deze methode aanroepen in de `Main` methode.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_client)]

## <a name="analyze-an-image"></a>Analyseer een afbeelding

Met de volgende code wordt een methode gedefinieerd, `AnalyzeImageUrl`, die gebruikmaakt van het-client object om een externe installatie kopie te analyseren en de resultaten af te drukken. De methode retourneert een beschrijving van de tekst, categorisatie, lijst met tags, gedetecteerde gezichten, inhouds vlaggen voor volwassenen, hoofd kleuren en afbeeldings type.

Voeg de methode aanroep toe aan de `Main` methode.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyzeinmain)]

### <a name="set-up-test-image"></a>Test installatie kopie instellen

Sla in de **programma** klasse een verwijzing op naar de URL van de afbeelding die u wilt analyseren.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyze_url)]

> [!NOTE]
> U kunt ook een lokale installatie kopie analyseren. Zie de voorbeeld code op [github](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs) voor scenario's met betrekking tot lokale installatie kopieën.

### <a name="specify-visual-features"></a>Visuele functies opgeven

Definieer uw nieuwe methode voor het analyseren van afbeeldingen. Voeg de onderstaande code toe. Hiermee geeft u de visuele functies op die u wilt extra heren in uw analyse. Zie de [VisualFeatureTypes](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet) -inventarisatie voor een volledige lijst.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_visualfeatures)]

### <a name="analyze"></a>Analyseren

De methode **AnalyzeImageAsync** retourneert een **ImageAnalysis** -object dat alle geëxtraheerde informatie bevat.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyze_call)]

In de volgende secties ziet u hoe u deze gegevens in detail kunt parseren.

### <a name="get-image-description"></a>Beschrijving van afbeelding ophalen

Met de volgende code wordt de lijst met gegenereerde bijschriften voor de afbeelding opgehaald. Zie [afbeeldingen beschrijven](../concept-describing-images.md) voor meer informatie.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_describe)]

### <a name="get-image-category"></a>Categorie van installatie kopie ophalen

Met de volgende code wordt de gedetecteerde categorie van de afbeelding opgehaald. Zie [installatie kopieën categoriseren](../concept-categorizing-images.md) voor meer informatie.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_categorize)]

### <a name="get-image-tags"></a>Afbeeldings Tags ophalen

Met de volgende code wordt de set gedetecteerde labels in de afbeelding opgehaald. Zie [inhouds Tags](../concept-tagging-images.md) voor meer informatie.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_tags)]

### <a name="detect-objects"></a>Objecten detecteren

Met de volgende code worden algemene objecten in de installatie kopie gedetecteerd en worden deze in de-console afgedrukt. Zie [object detectie](../concept-object-detection.md) voor meer informatie.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_objects)]

### <a name="detect-brands"></a>Brands detecteren

Met de volgende code worden de bedrijfs merken en-logo's in de installatie kopie gedetecteerd en afgedrukt in de-console. Zie [merk detectie](../concept-brand-detection.md) voor meer informatie.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_brands)]

### <a name="detect-faces"></a>Gezichten detecteren

Met de volgende code worden de gedetecteerde gezichten in de afbeelding met hun rechthoek coördinaten als resultaat gegeven en selecteert u face Attributes. Zie [gezichts detectie](../concept-detecting-faces.md) voor meer informatie.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Inhoud voor volwassenen, ongepaste of benchmarks detecteren

Met de volgende code wordt de gedetecteerde aanwezigheid van inhoud voor volwassenen afgedrukt in de installatie kopie. Zie [inhoud voor volwassenen, ongepaste, benchmarks](../concept-detecting-adult-content.md) voor meer informatie.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Kleuren schema afbeelding ophalen

Met de volgende code worden de gedetecteerde kleur kenmerken in de afbeelding afgedrukt, zoals de dominante kleuren en accent kleur. Zie [kleuren schema's](../concept-detecting-color-schemes.md) voor meer informatie.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Domein-specifieke inhoud ophalen

Computer Vision kunt gespecialiseerde modellen gebruiken om verdere analyse van installatie kopieën uit te voeren. Zie [Domain-specifieke inhoud](../concept-detecting-domain-content.md) voor meer informatie. 

Met de volgende code worden gegevens over gedetecteerde beroemdheden in de installatie kopie geparseerd.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_celebs)]

Met de volgende code worden gegevens over gedetecteerde bezienswaardigheden in de installatie kopie geparseerd.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Het afbeeldings type ophalen

Met de volgende code wordt informatie over het type afbeelding afgedrukt&mdash;of het een illustratie of een lijn tekening is.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Gedrukte en handgeschreven tekst lezen

Computer Vision kunt zicht bare tekst in een afbeelding lezen en deze converteren naar een teken stroom. De code in deze sectie definieert een methode, `ExtractTextUrl`, die het client object gebruikt voor het detecteren en uitpakken van gedrukte of handgeschreven tekst in de afbeelding.

Voeg de methode aanroep toe aan de `Main` methode.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extracttextinmain)]

### <a name="set-up-test-image"></a>Test installatie kopie instellen

Sla in de **programma** klasse een verwijzing op naar de URL van de afbeelding waaruit u tekst wilt extra heren.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extracttext_url)]

> [!NOTE]
> U kunt ook tekst extra heren uit een lokale installatie kopie. Zie de voorbeeld code op [github](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs) voor scenario's met betrekking tot lokale installatie kopieën.

### <a name="call-the-read-api"></a>De Lees-API aanroepen

Definieer de nieuwe methode voor het lezen van tekst. Voeg de onderstaande code toe, die de **BatchReadFileAsync** -methode voor de gegeven afbeelding aanroept. Hiermee wordt een bewerkings-ID geretourneerd en wordt een asynchroon proces gestart om de inhoud van de afbeelding te lezen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_call)]

### <a name="get-read-results"></a>Lees resultaten ophalen

Haal vervolgens de bewerkings-ID op die wordt geretourneerd door de **BatchReadFileAsync** -aanroep en gebruik deze om de service te doorzoeken op de resultaten van de bewerking. Met de volgende code wordt de bewerking met een interval van één seconde gecontroleerd totdat de resultaten worden geretourneerd. Vervolgens worden de geëxtraheerde tekst gegevens afgedrukt naar de-console.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_response)]

### <a name="display-read-results"></a>Lees resultaten weer geven

Voeg de volgende code toe om de opgehaalde tekst gegevens te parseren en weer te geven, en voltooi de methode definitie.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_display)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit vanuit de toepassingsmap met de opdracht `dotnet run`.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Computer Vision-API referentie (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet)

* [Wat is Computer Vision?](../Home.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs).
