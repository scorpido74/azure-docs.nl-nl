---
title: Quickstart voor Face .NET-clientbibliotheek voor .NET
description: Ga met deze quickstart aan de slag met de Face-clientbibliotheek voor .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 08/17/2020
ms.author: pafarley
ms.openlocfilehash: c243668f4ca1569ad05567649ad4a2498888847c
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88602522"
---
Ga aan de slag met de Face-clientbibliotheek voor .NET. Volg deze stappen om het pakket te installeren en de voorbeeldcode voor basistaken uit te proberen. De Face-service biedt u toegang tot geavanceerde algoritmen voor het detecteren en herkennen van menselijke gezichten in afbeeldingen.

Gebruik de Face-clientbibliotheek voor .NET voor het volgende:

* [Gezichten in een afbeelding detecteren](#detect-faces-in-an-image)
* [Vergelijkbare gezichten zoeken](#find-similar-faces)
* [Een persoonsgroep maken en trainen](#create-and-train-a-person-group)
* [Een gezicht identificeren](#identify-a-face)
* [Een momentopname maken voor gegevensmigratie](#take-a-snapshot-for-data-migration)

[Referentiedocumentatie](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet) | [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face) | [Pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.6.0-preview.1) | [Voorbeelden](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Vereisten

* De huidige versie van [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* Zodra u een Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Een Face-resource maken"  target="_blank">maakt u een Face-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in Azure Portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Face-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.
* Nadat u een sleutel en eindpunt hebt verkregen, gaat u [omgevingsvariabelen maken](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de sleutel en de eindpunt-URL, respectievelijk `FACE_SUBSCRIPTION_KEY` en `FACE_ENDPOINT` genaamd.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

Maak een nieuwe .NET Core-toepassing in uw favoriete editor of IDE. 

Gebruik in een consolevenster (zoals cmd, PowerShell of Bash) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam `face-quickstart`. Met deze opdracht maakt u een eenvoudig Hallo wereld-C#-project met één bronbestand: *Program.cs*. 

```dotnetcli
dotnet new console -n face-quickstart
```

Wijzig uw map in de zojuist gemaakte app-map. U kunt de toepassing maken met:

```dotnetcli
dotnet build
```

De build-uitvoer mag geen waarschuwingen of fouten bevatten. 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

Open vanuit de projectmap het bestand *Program.cs* in uw favoriete editor of IDE. Voeg de volgende `using`-instructies toe:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_using)]

Maak in de methode `Main` van de toepassing variabelen voor het Azure-eindpunt en de Azure-sleutel voor uw resource.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_mainvars)]

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Installeer in de toepassingsmap de Face-clientbibliotheek voor .NET met de volgende opdracht:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.6.0-preview.1
```

Als u de Visual Studio-IDE, gebruikt, is de clientbibliotheek beschikbaar als downloadbaar NuGet-pakket.

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Face .NET-clientbibliotheek:

|Naam|Beschrijving|
|---|---|
|[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) | Deze klasse vertegenwoordigt uw autorisatie voor het gebruik van de Face-service. U hebt deze nodig voor alle Face-functies. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om instanties van andere klassen te maken. |
|[FaceOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations?view=azure-dotnet)|Deze klasse verwerkt de basistaken voor detectie en herkenning die u met menselijke gezichten kunt uitvoeren. |
|[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)|Deze klasse vertegenwoordigt alle gegevens die zijn gedetecteerd van één gezicht in een afbeelding. U kunt deze gebruiken om gedetailleerde informatie over het gezicht op te halen.|
|[FaceListOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)|Deze klasse beheert de in de cloud opgeslagen **FaceList**-constructies, waarin een geassorteerde set gezichten wordt opgeslagen. |
|[PersonGroupPersonExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions?view=azure-dotnet)| Deze klasse beheert de in de cloud opgeslagen **Person**-constructies, die een set gezichten opslaan die tot één persoon behoren.|
|[PersonGroupOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)| Deze klasse beheert de in de cloud opgeslagen **PersonGroup**-constructies, die een set van verschillende **Person**-objecten opslaan. |
|[ShapshotOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)|Deze klasse beheert de functionaliteit van de momentopname. U kunt deze gebruiken om al uw op de cloud gebaseerde gezichtsgegevens tijdelijk op te slaan en deze gegevens te migreren naar een nieuw Azure-abonnement. |

## <a name="code-examples"></a>Codevoorbeelden

De onderstaande codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Face-clientbibliotheek voor .NET:

* [De client verifiëren](#authenticate-the-client)
* [Gezichten in een afbeelding detecteren](#detect-faces-in-an-image)
* [Vergelijkbare gezichten zoeken](#find-similar-faces)
* [Een persoonsgroep maken en trainen](#create-and-train-a-person-group)
* [Een gezicht identificeren](#identify-a-face)
* [Een momentopname maken voor gegevensmigratie](#take-a-snapshot-for-data-migration)


## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> In deze quickstart wordt ervan uitgegaan dat u [omgevingsvariabelen hebt gemaakt](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor uw Face-sleutel en -eindpunt, met de naam `FACE_SUBSCRIPTION_KEY` en `FACE_ENDPOINT`.

Instantieer in een nieuwe methode een client met uw eindpunt en sleutel. Maak een **[ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials?view=azure-dotnet)** -object met uw sleutel en maak hiermee en met uw eindpunt een **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** -object.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_auth)]

Waarschijnlijk wilt u deze methode aanroepen in de `Main`-methode.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_client)]

### <a name="declare-helper-fields"></a>Helper-velden declareren

De volgende velden zijn vereist voor verschillende gezichtsbewerkingen die u later toevoegt. Definieer de volgende URL-tekenreeks in de hoofdmap van uw klasse. Deze URL verwijst naar een map met voorbeelden.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_image_url)]

Definieer tekenreeksen die verwijzen naar de verschillende typen herkenningsmodellen. Later kunt u opgeven welk herkenningsmodel u wilt gebruiken voor gezichtsdetectie. Zie [Een herkenningsmodel opgeven](../../Face-API-How-to-Topics/specify-recognition-model.md) voor meer informatie over deze opties.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_models)]

## <a name="detect-faces-in-an-image"></a>Gezichten in een afbeelding detecteren

Voeg de volgende methode-aanroep toe aan uw **Main**-methode. U definieert later de methode. Voor de laatste detectiebewerking zijn een **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** -object, een afbeeldings-URL en een herkenningsmodel nodig.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_call)]

### <a name="get-detected-face-objects"></a>Gedetecteerde face-objecten ophalen

In het volgende codeblok detecteert de `DetectFaceExtract`-methode gezichten in drie van de afbeeldingen bij de opgegeven URL en wordt een lijst **[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** -objecten in het programmageheugen gemaakt. In de lijst met **[FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** -waarden wordt aangegeven welke functies moeten worden geëxtraheerd. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Gedetecteerde gezichtsgegevens weergeven

De rest van de methode `DetectFaceExtract` parseert en drukt de kenmerkgegevens af voor elk gedetecteerd gezicht. Elk kenmerk moet afzonderlijk worden opgegeven in de oorspronkelijke gezichtsdetectie-API-aanroep (in de lijst **[FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** ). De volgende code verwerkt elk kenmerk, maar u zult waarschijnlijk slechts één of enkele gebruiken.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>Vergelijkbare gezichten zoeken

De volgende code neemt één gedetecteerd gezicht (bron) en doorzoekt een set andere gezichten (doel) om overeenkomsten te vinden. Wanneer er een overeenkomst wordt gevonden, wordt de id van het overeenkomende gezicht afgedrukt op de console.

### <a name="detect-faces-for-comparison"></a>Gezichten detecteren voor vergelijking

Definieer eerst een tweede gezichtsdetectiemethode. U moet gezichten in afbeeldingen detecteren voordat u ze kunt vergelijken en deze detectiemethode is geoptimaliseerd voor vergelijkingsbewerkingen. Er worden geen gedetailleerde gezichtskenmerken geëxtraheerd, zoals in de bovenstaande sectie, en er wordt een ander herkenningsmodel gebruikt.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>Overeenkomsten zoeken

De volgende methode detecteert gezichten in een set doelafbeeldingen en in één bronafbeelding. Vervolgens worden deze vergeleken en worden alle doelafbeeldingen gevonden die vergelijkbaar zijn met de bronafbeelding.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>Overeenkomsten afdrukken

Met de volgende code worden de overeenkomende resultaten op de console weergegeven:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar_print)]

## <a name="identify-a-face"></a>Een gezicht identificeren

De bewerking Identificeren neemt een afbeelding van een persoon (of meerdere personen) en zoekt naar de identiteit van elk gezicht in de afbeelding. Elk gedetecteerd gezicht wordt vergeleken met een **PersonGroup**, een database van verschillende **Person**-objecten waarvan de gezichtskenmerken bekend zijn. Als u de bewerking Identificeren wilt uitvoeren, moet u eerst een **PersonGroup** maken en trainen

### <a name="create-and-train-a-person-group"></a>Een persoonsgroep maken en trainen

Met de volgende code wordt een **PersonGroup** gemaakt met zes verschillende **Person**-objecten. Het koppelt elke **persoon** aan een reeks voorbeeldafbeeldingen en wordt vervolgens getraind om elke persoon te herkennen aan de gezichtskenmerken. **Person**- en **PersonGroup**-objecten worden gebruikt bij de bewerkingen Verifiëren, Identificeren of Groeperen.

#### <a name="create-persongroup"></a>PersonGroup maken

Declareer een tekenreeksvariabele in de hoofdmap van uw klasse om de id weer te geven van de **PersonGroup** die u maakt.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_declare)]

Voeg in een nieuwe methode de volgende code toe. Met deze methode wordt de bewerking Identificeren uitgevoerd. Het eerste codeblok koppelt de namen van personen aan hun voorbeeldafbeeldingen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_files)]

Voeg vervolgens de volgende code toe om een **Person**-object te maken voor elke persoon in de woordenlijst en voeg de gezichtsgegevens toe van de juiste afbeeldingen. Elk **Person**-object is gekoppeld aan dezelfde **PersonGroup** via de unieke id-reeks. Vergeet niet om de variabelen `client`, `url`en `RECOGNITION_MODEL1` in deze methode op te geven.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_create)]

#### <a name="train-persongroup"></a>PersonGroup trainen

Zodra u de gezichtsgegevens van uw afbeeldingen hebt geëxtraheerd en deze in verschillende **Person**-objecten hebt gesorteerd, moet u de **PersonGroup** trainen om de visuele functies te identificeren die zijn gekoppeld aan elk van de **Person**-objecten. Met de volgende code wordt de asynchrone **Train**-methode aangeroepen en worden de resultaten gecontroleerd, en wordt de status naar de console afgedrukt.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_train)]

Deze **Person**-groep en de bijbehorende **Person**-objecten zijn nu klaar om te worden gebruikt in de bewerkingen Verifiëren, Identificeren of Groeperen.

### <a name="get-a-test-image"></a>Een testafbeelding ophalen

U ziet dat met de code voor [Een persoonsgroep maken en trainen](#create-and-train-a-person-group) een variabele `sourceImageFileName` wordt gedefinieerd. Deze variabele komt overeen met de bronafbeelding&mdash;de afbeelding die de personen bevat die moeten worden geïdentificeerd.

### <a name="identify-faces"></a>Gezichten identificeren

De volgende code neemt de bronafbeelding en maakt een lijst van alle gezichten die in de afbeelding zijn gedetecteerd. Dit zijn de gezichten die worden geïdentificeerd op basis van de **PersonGroup**.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify_sources)]

In het volgende codefragment wordt de bewerking **IdentifyAsync** aangeroepen en worden de resultaten naar de console afgedrukt. Hier probeert de service elk gezicht van de bronafbeelding te koppelen aan een **Person** in de opgegeven **PersonGroup**. Hiermee wordt uw methode Identificeren uitgesloten.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify)]

## <a name="take-a-snapshot-for-data-migration"></a>Een momentopname maken voor gegevensmigratie

Met de functie momentopnamen kunt u opgeslagen gezichtsgegevens, zoals een getrainde **PersonGroup**, verplaatsen naar een ander Azure Cognitive Services Face-abonnement. U kunt deze functie gebruiken als u bijvoorbeeld een **PersonGroup**-object hebt gemaakt met behulp van een gratis abonnement en wilt migreren naar een betaald abonnement. Zie [Uw gezichtsgegevens migreren](../../Face-API-How-to-Topics/how-to-migrate-face-data.md) voor een overzicht van de functie momentopnamen.

In dit voorbeeld migreert u de **PersonGroup** die u hebt gemaakt in [Een groep personen maken en trainen](#create-and-train-a-person-group). U kunt deze sectie eerst voltooien of uw eigen Face-gegevensconstructies maken om te migreren.

### <a name="set-up-target-subscription"></a>Doelabonnement instellen

Eerst moet u een tweede Azure-abonnement hebben met een Face-resource. U kunt dit doen door de stappen te volgen in het gedeelte [Instellen](#setting-up). 

Definieer vervolgens de volgende variabelen in de methode `Main` van uw programma. U moet nieuwe omgevingsvariabelen maken voor de abonnements-id van uw Azure-account, evenals de sleutel-, eindpunt- en abonnements-id van uw nieuwe (doel)account. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_vars)]

Declareer voor dit voorbeeld een variabele voor de id van het doelobject **​​PersonGroup**&mdash; dat bij het nieuwe abonnement hoort, waarnaar u uw gegevens kopieert.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_vars)]

### <a name="authenticate-target-client"></a>Doelclient verifiëren

Voeg vervolgens de code toe om uw tweede Face-abonnement te verifiëren.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_client)]

### <a name="use-a-snapshot"></a>Een momentopname gebruiken

De overige momentopnamebewerkingen moeten plaatsvinden binnen een asynchrone methode. 

1. De eerste stap is het **nemen van** de momentopname, waarmee de gezichtsgegevens van uw oorspronkelijke abonnement worden opgeslagen op een tijdelijke locatie van de Cloud. Deze methode retourneert een id die u gebruikt om de status van de bewerking op te vragen.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_take)]

1. Vervolgens moet u een query uitvoeren op de id totdat de bewerking is voltooid.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_take_wait)]

1. Gebruik vervolgens de bewerking **Toepassen** om uw gezichtsgegevens te schrijven naar uw doelabonnement. Met deze methode wordt ook een id-waarde geretourneerd.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_apply)]

1. Vervolgens moet u een query uitvoeren op de nieuwe id totdat de bewerking is voltooid.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_apply)]

1. Voltooi tot slot het try/catch-blok en voltooi de methode.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_trycatch)]

Op dit punt zou uw nieuwe **PersonGroup**-object dezelfde gegevens moeten hebben als het oorspronkelijke en toegankelijk moeten zijn vanaf uw nieuwe (doel) Azure Face-abonnement.

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing op vanuit uw toepassingsmap met de opdracht `dotnet run`.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Als u in deze quickstart een **PersonGroup** hebt gemaakt en u deze wilt verwijderen, voert u de volgende code uit in uw programma:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_delete)]

Definieer de verwijderingsmethode met de volgende code:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_deletepersongroup)]

Als u daarnaast gegevens hebt gemigreerd met behulp van de momentopnamefunctie in deze quickstart, moet u ook de **PersonGroup** verwijderen die is opgeslagen in het doelabonnement.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_target_persongroup_delete)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u de Face-bibliotheek voor .NET kunt gebruiken om basistaken uit te voeren. Bestudeer daarna het naslagmateriaal bij de Face-API voor meer informatie.

> [!div class="nextstepaction"]
> [Verwijzing naar de Face-API (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)

* [Wat is de Face-service?](../../overview.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs).
