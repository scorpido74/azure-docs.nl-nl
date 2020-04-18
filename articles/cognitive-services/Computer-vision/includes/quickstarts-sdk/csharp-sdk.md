---
title: 'Snelstart: Computer Vision-clientbibliotheek voor .NET'
description: Ga in deze quickstart aan de slag met de Computer Vision-clientbibliotheek voor .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: ce311d2deaf0eec76ef0dec3ea279a387d14fd22
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81637125"
---
<a name="HOLTop"></a>

[Voorbeeld van naslagdocumentatie](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet) | [Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision) | [(NuGet)-voorbeelden](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [Samples](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Instellen

### <a name="create-a-computer-vision-azure-resource"></a>Een Azure-bron voor Computer Vision maken

Azure Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een bron voor Computer Vision met de [Azure-portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine. U kunt ook het volgende doen:

* Ontvang een [proefsleutel](https://azure.microsoft.com/try/cognitive-services/#decision) die zeven dagen gratis geldig is. Nadat u zich hebt aangemeld, is deze beschikbaar op de [Azure-website.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Uw bron weergeven op de [Azure-portal.](https://portal.azure.com/)

Nadat u een sleutel uit uw proefabonnement of resource hebt opgehaald, maakt `COMPUTER_VISION_SUBSCRIPTION_KEY` u `COMPUTER_VISION_ENDPOINT` [omgevingsvariabelen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor respectievelijk de naam van de sleutel- en eindpunt-URL.

### <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

Maak een nieuwe .NET Core-toepassing in uw voorkeurseditor of IDE. 

Gebruik de `dotnet new` opdracht om in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe console-app met de naam `computer-vision-quickstart`te maken. Met deze opdracht wordt een eenvoudig "Hello World" C#-project gemaakt met één bronbestand: *Program.cs*.

```console
dotnet new console -n computer-vision-quickstart
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

Open in de projectmap het *Program.cs* bestand in uw voorkeurseditor of IDE. Voeg de `using` volgende richtlijnen toe:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_using)]

Maak in de klasse **Programma** van de toepassing variabelen voor het Azure-eindpunt en de sleutel van uw bron.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_vars)]

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Installeer in de toepassingsmap de clientbibliotheek Computer Vision voor .NET met de volgende opdracht:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 5.0.0
```

Als u de Visual Studio IDE gebruikt, is de clientbibliotheek beschikbaar als downloadbaar NuGet-pakket.

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces behandelen enkele van de belangrijkste functies van de Computer Vision .NET SDK.

|Naam|Beschrijving|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet) | Deze klasse is nodig voor alle Computer Vision-functionaliteit. U wilt deze instantiëren met uw abonnementsgegevens en u gebruikt deze om de meeste beeldbewerkingen uit te voeren.|
|[ComputerVisionClientExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions?view=azure-dotnet)| Deze klasse bevat aanvullende methoden voor de **ComputerVisionClient**.|
|[VisualFeatureTypes](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)| Dit enum definieert de verschillende soorten beeldanalyse die kunnen worden uitgevoerd in een standaard analysebewerking. U geeft een set VisualFeatureTypes-waarden op, afhankelijk van uw behoeften. |

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken uitvoeren met de computervision-clientbibliotheek voor .NET:

* [De client verifiëren](#authenticate-the-client)
* [Een afbeelding analyseren](#analyze-an-image)
* [Gedrukte en handgeschreven tekst lezen](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> Met deze quickstart wordt ervan uitgegaan dat u [omgevingsvariabelen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) hebt `COMPUTER_VISION_SUBSCRIPTION_KEY` gemaakt `COMPUTER_VISION_ENDPOINT` voor uw Computer Vision-sleutel en eindpunt, met de naam en respectievelijk voor.

In een nieuwe methode u een klant instantiëren met uw eindpunt en sleutel. Maak een **[Object ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials?view=azure-dotnet)** met uw sleutel en gebruik het met uw eindpunt om een **[ComputerVisionClient-object](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet)** te maken.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_auth)]

U zult waarschijnlijk deze methode in `Main` de methode willen aanroepen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_client)]

## <a name="analyze-an-image"></a>Een afbeelding analyseren

De volgende code definieert een methode, `AnalyzeImageUrl`die het clientobject gebruikt om een externe afbeelding te analyseren en de resultaten af te drukken. De methode retourneert een tekstbeschrijving, categorisering, lijst met tags, gedetecteerde gezichten, vlaggen met inhoud voor volwassenen, hoofdkleuren en afbeeldingstype.

Voeg de methodeaanroep toe aan uw `Main` methode.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyzeinmain)]

### <a name="set-up-test-image"></a>Testafbeelding instellen

Sla in de klasse **Programma** een verwijzing op naar de URL van de afbeelding die u wilt analyseren.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyze_url)]

> [!NOTE]
> U ook een lokale afbeelding analyseren. Zie de voorbeeldcode op [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs) voor scenario's met lokale afbeeldingen.

### <a name="specify-visual-features"></a>Visuele functies opgeven

Definieer uw nieuwe methode voor beeldanalyse. Voeg de onderstaande code toe, waarin visuele functies worden opgegeven die u wilt extraheren in uw analyse. Zie het **[VisualFeatureTypes-enum](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)** voor een volledige lijst.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_visualfeatures)]

Voeg een van de volgende codeblokken in de **methode AnalyzeImageUrl** om hun functies te implementeren. Vergeet niet om een sluitbeugel toe te voegen aan het einde.

```csharp
}
```

### <a name="analyze"></a>Analyseren

Met de methode **AnalyzeImageAsync** retourneert u een object **ImageAnalysis** dat alle geëxtraheerde informatie bevat.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyze_call)]

In de volgende secties wordt uitgelegd hoe u deze informatie in detail ontzien.

### <a name="get-image-description"></a>Afbeeldingsbeschrijving downloaden

De volgende code krijgt de lijst met gegenereerde bijschriften voor de afbeelding. Zie [Afbeeldingen beschrijven](../../concept-describing-images.md) voor meer details.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_describe)]

### <a name="get-image-category"></a>Afbeeldingscategorie oppakken

De volgende code krijgt de gedetecteerde categorie van de afbeelding. Zie [Afbeeldingen categoriseren](../../concept-categorizing-images.md) voor meer informatie.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_categorize)]

### <a name="get-image-tags"></a>Afbeeldingstags opmaken

De volgende code krijgt de set gedetecteerde tags in de afbeelding. Zie [Inhoudstags](../../concept-tagging-images.md) voor meer informatie.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_tags)]

### <a name="detect-objects"></a>Objecten detecteren

De volgende code detecteert algemene objecten in de afbeelding en drukt deze af op de console. Zie [Objectdetectie](../../concept-object-detection.md) voor meer informatie.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_objects)]

### <a name="detect-brands"></a>Merken detecteren

De volgende code detecteert bedrijfsmerken en logo's in de afbeelding en drukt deze af op de console. Zie [Merkdetectie](../../concept-brand-detection.md) voor meer details.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_brands)]

### <a name="detect-faces"></a>Gezichten detecteren

Met de volgende code worden de gedetecteerde gezichten in de afbeelding geretourneerd met hun rechthoekcoördinaten en selecteert u gezichtskenmerken. Zie [Gezichtsherkenning](../../concept-detecting-faces.md) voor meer details.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Detecteer inhoud voor volwassenen, racy of bloederige

Met de volgende code wordt de gedetecteerde aanwezigheid van inhoud voor volwassenen in de afbeelding afgedrukt. Zie [Adult, racy, bloederige inhoud](../../concept-detecting-adult-content.md) voor meer details.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Afbeeldingskleurenschema krijgen

In de volgende code worden de gedetecteerde kleurkenmerken in de afbeelding afgedrukt, zoals de dominante kleuren en accentkleur. Zie [Kleurenschema's](../../concept-detecting-color-schemes.md) voor meer informatie.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Domeinspecifieke inhoud opmaken

Computer Vision kan gespecialiseerde modellen gebruiken om verdere analyse van beelden te doen. Zie [Domeinspecifieke inhoud](../../concept-detecting-domain-content.md) voor meer informatie. 

De volgende code ontleedt gegevens over gedetecteerde beroemdheden in de afbeelding.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_celebs)]

De volgende code onteert gegevens over gedetecteerde oriëntatiepunten in de afbeelding.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Het afbeeldingstype weergeven

In de volgende code wordt&mdash;informatie over het type afbeelding afgedrukt, ongeacht of het om illustraties of een lijntekening gaat.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Gedrukte en handgeschreven tekst lezen

Computer Vision kan zichtbare tekst in een afbeelding lezen en converteren naar een tekenstroom. De code in deze sectie `ExtractTextUrl`definieert een methode, die het clientobject gebruikt om afgedrukte of handgeschreven tekst in de afbeelding te detecteren en te extraheren.

Voeg de methodeaanroep toe aan uw `Main` methode.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extracttextinmain)]

### <a name="set-up-test-image"></a>Testafbeelding instellen

Sla in de klasse **Programma** een verwijzing op naar de URL van de afbeelding waaruit u tekst wilt extraheren.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extracttext_url)]

> [!NOTE]
> U ook tekst uit een lokale afbeelding extraheren. Zie de voorbeeldcode op [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs) voor scenario's met lokale afbeeldingen.

### <a name="call-the-read-api"></a>De API lezen aanroepen

Definieer de nieuwe methode voor het lezen van tekst. Voeg de onderstaande code toe, die de **batchreadfileasync-methode** voor de gegeven afbeelding aanroept. Hiermee wordt een bewerkings-id geretourneerd en wordt een asynchrone proces gestart om de inhoud van de afbeelding te lezen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_call)]

### <a name="get-read-results"></a>Lees resultaten

Download vervolgens de bewerkings-id terug van de **BatchReadFileAsync-aanroep** en gebruik deze om de service op te vragen voor bewerkingsresultaten. De volgende code controleert de bewerking met intervallen van één seconde totdat de resultaten zijn geretourneerd. Vervolgens worden de geëxtraheerde tekstgegevens naar de console afgedrukt.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_response)]

### <a name="display-read-results"></a>Leesresultaten weergeven

Voeg de volgende code toe aan parse en geef de opgehaalde tekstgegevens weer en voltooi de methodedefinitie.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_display)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit uw `dotnet run` toepassingsmap uit met de opdracht.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een abonnement voor Cognitive Services wilt opschonen en verwijderen, u de bron- of brongroep verwijderen. Als u de brongroep verwijdert, worden ook andere bronnen verwijderd die eraan zijn gekoppeld.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Verwijzing naar computervisie-API (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet)

* [Wat is Computer Vision?](../../Home.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub.](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs)
