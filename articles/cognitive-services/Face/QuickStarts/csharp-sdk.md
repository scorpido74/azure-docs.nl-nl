---
title: 'Snelstartgids: Face client library voor .NET'
description: Ga met deze Snelstartgids aan de slag met de face-client bibliotheek voor .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: e305a5634aa0c065342e1873c413039eb734b972
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76165891"
---
# <a name="quickstart-face-client-library-for-net"></a>Snelstartgids: Face client library voor .NET

Ga aan de slag met de face-client bibliotheek voor .NET. Volg deze stappen om het pakket te installeren en de voorbeeld code voor basis taken uit te proberen. De face-service biedt u toegang tot geavanceerde algoritmen voor het detecteren en herkennen van menselijke gezichten in afbeeldingen.

Gebruik de face-client bibliotheek voor .NET voor het volgende:

* [De client verifiëren](#authenticate-the-client)
* [Gezichten detecteren in een installatie kopie](#detect-faces-in-an-image)
* [Vergelijk bare gezichten zoeken](#find-similar-faces)
* [Een persoons groep maken en trainen](#create-and-train-a-person-group)
* [Een gezicht identificeren](#identify-a-face)
* [Een moment opname maken voor gegevens migratie](#take-a-snapshot-for-data-migration)

[Referentie documentatie](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet) | [bibliotheek bron code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face) | [pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.5.0-preview.1) | [beelden](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [.net core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Instellen

### <a name="create-a-face-azure-resource"></a>Een gezichts-Azure-resource maken

Azure-Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een resource voor gezicht met behulp van de [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure cli](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine. U kunt ook het volgende doen:

* Ontvang een [proef sleutel](https://azure.microsoft.com/try/cognitive-services/#decision) die zeven dagen gratis geldig is. Nadat u zich hebt aangemeld, is deze beschikbaar op de [Azure-website](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Bekijk uw resource op het [Azure Portal](https://portal.azure.com/).

Nadat u een sleutel van uw proef abonnement of resource hebt ontvangen, [maakt u een omgevings variabele](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de sleutel en eind punt-URL, respectievelijk met de naam `FACE_SUBSCRIPTION_KEY` en `FACE_ENDPOINT`.

### <a name="create-a-new-c-application"></a>Een nieuwe C# toepassing maken

Maak een nieuwe .NET core-toepassing in uw voorkeurs editor of IDE. 

Gebruik in een console venster (zoals cmd, Power shell of bash) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam `face-quickstart`. Met deze opdracht maakt u een eenvoudig ' C# Hallo wereld '-project met één bron bestand: *Program.cs*. 

```dotnetcli
dotnet new console -n face-quickstart
```

Wijzig uw directory in de zojuist gemaakte app-map. U kunt de toepassing samen stellen met:

```dotnetcli
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

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_using)]

Maak in de `Main` methode van de toepassing variabelen voor het Azure-eind punt en de sleutel van uw resource.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_mainvars)]

### <a name="install-the-client-library"></a>De client bibliotheek installeren

Installeer in de toepassingsmap de face-client bibliotheek voor .NET met de volgende opdracht:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.5.0-preview.1
```

Als u de Visual Studio IDE gebruikt, is de client bibliotheek beschikbaar als een downloadbaar NuGet-pakket.

## <a name="object-model"></a>Object model

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de face .NET SDK:

|Name|Beschrijving|
|---|---|
|[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) | Deze klasse vertegenwoordigt uw autorisatie voor het gebruik van de face-service en u hebt deze nodig voor alle gezichts functionaliteit. U maakt de app met uw abonnements gegevens en gebruikt deze om instanties van andere klassen te maken. |
|[FaceOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations?view=azure-dotnet)|Deze klasse verwerkt de basis taken voor detectie en herkenning die u met menselijke gezichten kunt uitvoeren. |
|[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)|Deze klasse vertegenwoordigt alle gegevens die zijn gedetecteerd van één gezicht in een afbeelding. U kunt deze gebruiken om gedetailleerde informatie over het gezicht op te halen.|
|[FaceListOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)|Deze klasse beheert de in de cloud opgeslagen **FaceList** -constructies, waarin een geassorteerde set gezichten wordt opgeslagen. |
|[PersonGroupPersonExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions?view=azure-dotnet)| Deze klasse beheert de door de cloud opgeslagen **persoons** constructies, waarin een set gezichten wordt opgeslagen die deel uitmaakt van één persoon.|
|[PersonGroupOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)| Deze klasse beheert de in de cloud opgeslagen **PersonGroup** -constructies, die een set geassorteerde **persoons** objecten opslaan. |
|[ShapshotOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)|Deze klasse beheert de functionaliteit van de moment opname. U kunt deze gebruiken om al uw op de cloud gebaseerde gezichts gegevens tijdelijk op te slaan en deze gegevens te migreren naar een nieuw Azure-abonnement. |

## <a name="code-examples"></a>Codevoorbeelden

In de onderstaande code fragmenten ziet u hoe u de volgende taken kunt uitvoeren met de face-client bibliotheek voor .NET:

* [De client verifiëren](#authenticate-the-client)
* [Gezichten detecteren in een installatie kopie](#detect-faces-in-an-image)
* [Vergelijk bare gezichten zoeken](#find-similar-faces)
* [Een persoons groep maken en trainen](#create-and-train-a-person-group)
* [Een gezicht identificeren](#identify-a-face)
* [Een moment opname maken voor gegevens migratie](#take-a-snapshot-for-data-migration)


## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> In deze Quick Start wordt ervan uitgegaan dat u [omgevings variabelen hebt gemaakt](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor uw gezichts sleutel en eind punt, met de naam `FACE_SUBSCRIPTION_KEY` en `FACE_ENDPOINT`.

In een nieuwe methode maakt u een exemplaar van een client met uw eind punt en sleutel. Maak een [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) -object met uw sleutel en gebruik het met uw eind punt om een [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) -object te maken.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_auth)]

Waarschijnlijk wilt u deze methode aanroepen in de `Main` methode.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_client)]

## <a name="detect-faces-in-an-image"></a>Gezichten in een afbeelding detecteren

Definieer de volgende URL-teken reeks in de hoofdmap van uw klasse. Deze URL verwijst naar een aantal voorbeeld afbeeldingen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_image_url)]

Optioneel kunt u kiezen welk AI-model u wilt gebruiken om gegevens van de gedetecteerde zijde (s) te extra heren. Zie [een herkennings model opgeven](../Face-API-How-to-Topics/specify-recognition-model.md) voor meer informatie over deze opties.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_models)]

Bij de laatste detectie bewerking worden een [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) -object, een afbeeldings-URL en een herkennings model uitgevoerd.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_call)]

### <a name="get-detected-face-objects"></a>Gedetecteerde face-objecten ophalen

In het volgende code blok detecteert de `DetectFaceExtract`-methode gezichten in drie van de afbeeldingen bij de opgegeven URL en maakt een lijst met [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) -objecten in programma geheugen. De lijst met [FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) -waarden geeft aan welke functies moeten worden geëxtraheerd. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Gedetecteerde gezichts gegevens weer geven

De rest van de methode `DetectFaceExtract` parseert en drukt de kenmerk gegevens af voor elk gedetecteerd gezicht. Elk kenmerk moet afzonderlijk worden opgegeven in de oorspronkelijke gezichts detectie-API-aanroep (in de [FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) -lijst). De volgende code verwerkt elk kenmerk, maar u zult waarschijnlijk slechts één of enkele gebruiken.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>Vergelijkbare gezichten zoeken

De volgende code neemt één gedetecteerd gezicht (bron) en doorzoekt een set andere gezichten (doel) om overeenkomsten te vinden. Wanneer er een overeenkomst wordt gevonden, wordt de ID van het overeenkomende gezicht afgedrukt op de console.

### <a name="detect-faces-for-comparison"></a>Gezichten detecteren voor vergelijking

Definieer eerst een tweede gezichts detectie methode. U moet gezichten in installatie kopieën detecteren voordat u ze kunt vergelijken en deze detectie methode is geoptimaliseerd voor vergelijkings bewerkingen. Er worden geen gedetailleerde gezichts kenmerken geëxtraheerd, zoals in de bovenstaande sectie, en er wordt een ander herkennings model gebruikt.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>Overeenkomsten zoeken

De volgende methode detecteert gezichten in een set doel installatie kopieën en in één bron installatie kopie. Vervolgens worden deze vergeleken en worden alle doel installatie kopieën gevonden die vergelijkbaar zijn met de bron installatie kopie.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>Overeenkomsten afdrukken

Met de volgende code worden de overeenkomende gegevens in de-console afgedrukt:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_find_similar_print)]

## <a name="create-and-train-a-person-group"></a>Een persoons groep maken en trainen

Met de volgende code wordt een **PersonGroup** gemaakt met zes verschillende **personen** -objecten. Elke **persoon** met een set voorbeeld afbeeldingen wordt gekoppeld en vervolgens wordt de treinen door hun gezichts kenmerken te herkennen. De objecten **person** en **PersonGroup** worden gebruikt in de bewerkingen controleren, identificeren en groeperen.

Als u dit nog niet hebt gedaan, definieert u de volgende URL-teken reeks in de hoofdmap van uw klasse. Dit wijst naar een set voorbeeld afbeeldingen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_image_url)]

In de code verderop in deze sectie wordt een herkennings model opgegeven voor het extra heren van gegevens van gezichten. het volgende fragment maakt verwijzingen naar de beschik bare modellen. Zie [een herkennings model opgeven](../Face-API-How-to-Topics/specify-recognition-model.md) voor informatie over herkennings modellen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_models)]

### <a name="create-persongroup"></a>PersonGroup maken

Declareer een teken reeks variabele in de hoofdmap van uw klasse om de ID weer te geven van de **PersonGroup** die u maakt.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_declare)]

Voeg de volgende code toe aan een nieuwe methode. Deze code koppelt de namen van personen aan hun voorbeeld afbeeldingen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_files)]

Voeg vervolgens de volgende code toe om een **persoons** object te maken voor elke persoon in de woorden lijst en voeg de gezichts gegevens toe van de juiste installatie kopieën. Elk **persoons** object is gekoppeld aan dezelfde **PersonGroup** via de unieke id-reeks. Vergeet niet om de variabelen `client`, `url`en `RECOGNITION_MODEL1` door te geven aan deze methode.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_create)]

### <a name="train-persongroup"></a>PersonGroup trainen

Zodra u de gezichts gegevens van uw afbeeldingen hebt geëxtraheerd en deze in verschillende **persoons** objecten hebt gesorteerd, moet u de **PersonGroup** trainen om de visuele functies te identificeren die aan elk van zijn **persoons** objecten zijn gekoppeld. Met de volgende code wordt de asynchrone **trein** methode aangeroepen en worden de resultaten gecontroleerd en wordt de status naar de console afgedrukt.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_train)]

Deze **persoons** groep en de bijbehorende **persoons** objecten zijn nu klaar om te worden gebruikt in de bewerkingen controleren, identificeren of groeperen.

## <a name="identify-a-face"></a>Een gezicht identificeren

Bij de identificerende bewerking wordt een afbeelding van een persoon (of meerdere personen) gebruikt en wordt gezocht naar de identiteit van elk gezicht in de installatie kopie. Elk gedetecteerd gezicht wordt vergeleken met een **PersonGroup**, een Data Base van verschillende **personen** -objecten waarvan de gezichts kenmerken bekend zijn.

> [!IMPORTANT]
> Als u dit voor beeld wilt uitvoeren, moet u eerst de code uitvoeren in [een persoons groep maken en trainen](#create-and-train-a-person-group). De variabelen die in deze sectie worden gebruikt&mdash;`client`, `url`en `RECOGNITION_MODEL1`&mdash;moeten hier ook beschikbaar zijn.

### <a name="get-a-test-image"></a>Een test installatie kopie ophalen

U ziet dat de code voor het [maken en trainen van een persoons groep](#create-and-train-a-person-group) een variabele `sourceImageFileName`definieert. Deze variabele komt overeen met de bron afbeelding&mdash;de afbeelding die de personen bevat die moeten worden geïdentificeerd.

### <a name="identify-faces"></a>Gezichten identificeren

De volgende code neemt de bron installatie kopie en maakt een lijst van alle gezichten die in de installatie kopie zijn gedetecteerd. Dit zijn de gezichten die worden geïdentificeerd op basis van de **PersonGroup**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_identify_sources)]

In het volgende code fragment wordt de identificerende bewerking aangeroepen en worden de resultaten afgedrukt in de-console. Hier probeert de service elk gezicht van de bron installatie kopie te koppelen aan een **persoon** in de opgegeven **PersonGroup**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_identify)]

## <a name="take-a-snapshot-for-data-migration"></a>Een moment opname maken voor gegevens migratie

Met de functie moment opnamen kunt u opgeslagen gezichts gegevens, zoals een getraind **PersonGroup**, verplaatsen naar een ander Azure Cognitive Services Face-abonnement. U kunt deze functie gebruiken als u bijvoorbeeld een **PersonGroup** -object hebt gemaakt met behulp van een gratis proef abonnement en wilt migreren naar een betaald abonnement. Zie [uw gezichts gegevens migreren](../Face-API-How-to-Topics/how-to-migrate-face-data.md) voor een overzicht van de functie moment opnamen.

In dit voor beeld migreert u de **PersonGroup** die u hebt gemaakt in [een persoons groep maken en trainen](#create-and-train-a-person-group). U kunt deze sectie eerst volt ooien of uw eigen face data-construct (s) maken om te migreren.

### <a name="set-up-target-subscription"></a>Doel abonnement instellen

Eerst moet u een tweede Azure-abonnement hebben met een face-resource. u kunt dit doen door de stappen in de sectie [instellen](#setting-up) te volgen. 

Definieer vervolgens de volgende variabelen in de `Main` methode van uw programma. U moet nieuwe omgevings variabelen maken voor de abonnements-ID van uw Azure-account, evenals de sleutel, het eind punt en de abonnements-ID van uw nieuwe (doel) account. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_vars)]

Voor dit voor beeld declareert u een variabele voor de ID van de doel- **PersonGroup**&mdash;het object dat bij het nieuwe abonnement hoort, waarnaar u uw gegevens gaat kopiëren.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_vars)]

### <a name="authenticate-target-client"></a>Doel-client verifiëren

Voeg vervolgens de code toe om uw abonnement op secundair gezicht te verifiëren.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_client)]

### <a name="use-a-snapshot"></a>Een moment opname gebruiken

De rest van de momentopname bewerkingen moeten plaatsvinden binnen een asynchrone methode. 

1. De eerste stap is het **maken** van de moment opname, waarmee de gezichts gegevens van uw oorspronkelijke abonnement worden opgeslagen op een tijdelijke locatie van de Cloud. Deze methode retourneert een ID die u gebruikt om de status van de bewerking op te vragen.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_take)]

1. Vervolgens moet u een query uitvoeren op de ID totdat de bewerking is voltooid.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_take_wait)]

1. Gebruik vervolgens de bewerking **Apply** om uw gezichts gegevens te schrijven naar uw doel abonnement. Met deze methode wordt ook een ID-waarde geretourneerd.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_apply)]

1. Daarna moet u een query uitvoeren op de nieuwe ID totdat de bewerking is voltooid.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_apply)]

1. Ten slotte voltooit u het try/catch-blok en voltooit u de-methode.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_trycatch)]

Op dit moment moeten uw nieuwe **PersonGroup** -object dezelfde gegevens hebben als het origineel en moeten ze toegankelijk zijn vanuit uw nieuwe (doel) Azure face-abonnement.

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit vanuit de toepassingsmap met de opdracht `dotnet run`.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Als u in deze Quick Start een **PersonGroup** hebt gemaakt en u deze wilt verwijderen, voert u de volgende code uit in het programma:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_delete)]

Definieer de verwijderings methode met de volgende code:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_deletepersongroup)]

Als u gegevens hebt gemigreerd met behulp van de snap shot-functie in deze Quick Start, moet u ook de **PersonGroup** verwijderen die is opgeslagen in het doel abonnement.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_target_persongroup_delete)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u de face-bibliotheek voor .NET kunt gebruiken om basis taken uit te voeren. Bekijk vervolgens de referentie documentatie voor meer informatie over de-bibliotheek.

> [!div class="nextstepaction"]
> [Face-API referentie (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)

* [Wat is de face-service?](../overview.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/Face/Program.cs).
