---
title: 'Snelstart: Face-clientbibliotheek voor .NET'
description: Ga aan de slag met de Face-clientbibliotheek voor .NET met deze quickstart.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: e51937dfe2afa0e92ce98b4c305555b53896e5f1
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78401780"
---
# <a name="quickstart-face-client-library-for-net"></a>Snelstart: Face-clientbibliotheek voor .NET

Ga aan de slag met de Face-clientbibliotheek voor .NET. Volg deze stappen om het pakket te installeren en probeer de voorbeeldcode voor basistaken uit. De Face-service biedt u toegang tot geavanceerde algoritmen voor het detecteren en herkennen van menselijke gezichten in afbeeldingen.

Gebruik de Face-clientbibliotheek voor .NET om:

* [Gezichten in een afbeelding detecteren](#detect-faces-in-an-image)
* [Vergelijkbare gezichten zoeken](#find-similar-faces)
* [Een personengroep maken en trainen](#create-and-train-a-person-group)
* [Een gezicht identificeren](#identify-a-face)
* [Een momentopname maken voor gegevensmigratie](#take-a-snapshot-for-data-migration)

[Voorbeeld van naslagdocumentatie](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet) | [Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face) | [(NuGet)-voorbeelden](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.5.0-preview.1) | [Samples](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Instellen

### <a name="create-a-face-azure-resource"></a>Een Face Azure-bron maken

Azure Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een bron voor Face met de [Azure-portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine. U kunt ook het volgende doen:

* Ontvang een [proefsleutel](https://azure.microsoft.com/try/cognitive-services/#decision) die zeven dagen gratis geldig is. Nadat u zich hebt aangemeld, is deze beschikbaar op de [Azure-website.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Uw bron weergeven op de [Azure-portal.](https://portal.azure.com/)

Nadat u een sleutel uit uw proefabonnement of resource hebt opgehaald, maakt `FACE_SUBSCRIPTION_KEY` u `FACE_ENDPOINT` [een omgevingsvariabele](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor respectievelijk de naam van de sleutel- en eindpunt-URL.

### <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

Maak een nieuwe .NET Core-toepassing in uw voorkeurseditor of IDE. 

Gebruik de `dotnet new` opdracht om in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe console-app met de naam `face-quickstart`te maken. Met deze opdracht wordt een eenvoudig "Hello World" C#-project gemaakt met één bronbestand: *Program.cs*. 

```dotnetcli
dotnet new console -n face-quickstart
```

Wijzig uw map in de nieuw gemaakte app-map. U de toepassing bouwen met:

```dotnetcli
dotnet build
```

De buildoutput mag geen waarschuwingen of fouten bevatten. 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

Open in de projectmap het *Program.cs* bestand in uw voorkeurseditor of IDE. Voeg de `using` volgende richtlijnen toe:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_using)]

Maak in de `Main` toepassingsmethode variabelen voor het Azure-eindpunt en de sleutel van uw bron.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_mainvars)]

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Installeer in de toepassingsmap de Face-clientbibliotheek voor .NET met de volgende opdracht:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.5.0-preview.1
```

Als u de Visual Studio IDE gebruikt, is de clientbibliotheek beschikbaar als downloadbaar NuGet-pakket.

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces behandelen enkele van de belangrijkste functies van de Face .NET SDK:

|Name|Beschrijving|
|---|---|
|[FaceClient (FaceClient)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) | Deze klasse vertegenwoordigt uw autorisatie om de Face-service te gebruiken en u hebt deze nodig voor alle Face-functionaliteit. U wilt deze met uw abonnementsgegevens en u gebruikt deze om exemplaren van andere klassen te produceren. |
|[FaceOperations (FaceOperations)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations?view=azure-dotnet)|Deze klasse behandelt de basisdetectie- en herkenningstaken die u uitvoeren met menselijke gezichten. |
|[GedetecteerdGezicht](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)|Deze klasse vertegenwoordigt alle gegevens die zijn gedetecteerd vanaf één gezicht in een afbeelding. U het gebruiken om gedetailleerde informatie over het gezicht op te halen.|
|[FaceListOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)|Deze klasse beheert de door de cloud opgeslagen **FaceList-constructies,** die een diverse set gezichten opslaan. |
|[PersonGroupPersonExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions?view=azure-dotnet)| Deze klasse beheert de **Person** door de cloud opgeslagen persoonsconstructies, die een reeks gezichten opslaan die van één persoon zijn.|
|[PersonGroupOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)| Deze klasse beheert de door de cloud opgeslagen **PersonGroup-constructies,** waarmee een set diverse **persoonsobjecten wordt** opgeslagen. |
|[ShapshotOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)|Deze klasse beheert de snapshot-functionaliteit. U het gebruiken om al uw op de cloud gebaseerde Face-gegevens tijdelijk op te slaan en die gegevens te migreren naar een nieuw Azure-abonnement. |

## <a name="code-examples"></a>Codevoorbeelden

In de onderstaande codefragmenten ziet u hoe u de volgende taken uitvoeren met de Face-clientbibliotheek voor .NET:

* [De client verifiëren](#authenticate-the-client)
* [Gezichten in een afbeelding detecteren](#detect-faces-in-an-image)
* [Vergelijkbare gezichten zoeken](#find-similar-faces)
* [Een personengroep maken en trainen](#create-and-train-a-person-group)
* [Een gezicht identificeren](#identify-a-face)
* [Een momentopname maken voor gegevensmigratie](#take-a-snapshot-for-data-migration)


## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> In deze snelstart wordt ervan uitgegaan dat u [omgevingsvariabelen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) hebt `FACE_SUBSCRIPTION_KEY` `FACE_ENDPOINT`gemaakt voor uw Face-toets en eindpunt, met de naam en .

In een nieuwe methode u een klant instantiëren met uw eindpunt en sleutel. Maak een **[Object ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials?view=azure-dotnet)** met uw sleutel en gebruik het met uw eindpunt om een **[FaceClient-object](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** te maken.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_auth)]

U zult waarschijnlijk deze methode in `Main` de methode willen aanroepen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_client)]

## <a name="detect-faces-in-an-image"></a>Gezichten in een afbeelding detecteren

Definieer aan de hoofdmap van uw klas de volgende URL-tekenreeks. Deze URL verwijst naar een reeks voorbeeldafbeeldingen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_image_url)]

Optioneel u kiezen welk AI-model u wilt gebruiken om gegevens uit het gedetecteerde gezicht(en) te extraheren. Zie [Een herkenningsmodel opgeven](../Face-API-How-to-Topics/specify-recognition-model.md) voor informatie over deze opties.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_models)]

Met de laatste bewerking Detecteren wordt een **[FaceClient-object,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** een afbeeldings-URL en een herkenningsmodel nodig.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_call)]

### <a name="get-detected-face-objects"></a>Gedetecteerde gezichtsobjecten zoeken

In het volgende codeblok `DetectFaceExtract` detecteert de methode gezichten in drie van de afbeeldingen op de opgegeven URL en maakt een lijst met **[Gedetecteerde Face-objecten](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** in het programmageheugen. De lijst met **[FaceAttributeType-waarden](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** geeft aan welke functies moeten worden geëxtraheerd. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Gedetecteerde gezichtsgegevens weergeven

De rest `DetectFaceExtract` van de methode onteinden en drukt de attribuutgegevens af voor elk gedetecteerd gezicht. Elk kenmerk moet afzonderlijk worden opgegeven in de oorspronkelijke API-aanroep voor gezichtsherkenning (in de lijst **[FaceAttributeType).](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** De volgende code verwerkt elk kenmerk, maar u hoeft er waarschijnlijk slechts één of een paar te gebruiken.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>Vergelijkbare gezichten zoeken

De volgende code neemt een enkel gedetecteerd gezicht (bron) en zoekt een set van andere gezichten (doel) om overeenkomsten te vinden. Wanneer het een overeenkomst vindt, drukt het de IDENTITEITSKAART van het gematchte gezicht aan de console af.

### <a name="detect-faces-for-comparison"></a>Gezichten detecteren voor vergelijking

Definieer eerst een tweede gezichtsherkenningsmethode. U moet gezichten in afbeeldingen detecteren voordat u ze vergelijken en deze detectiemethode is geoptimaliseerd voor vergelijkingsbewerkingen. Het haalt geen gedetailleerde gezichtskenmerken zoals in de sectie hierboven, en het gebruikt een verschillend erkenningsmodel.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>Zoek overeenkomsten

De volgende methode detecteert gezichten in een reeks doelafbeeldingen en in één bronafbeelding. Vervolgens wordt ze vergeleken en worden alle doelafbeeldingen gevonden die vergelijkbaar zijn met de bronafbeelding.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>Overeenkomen afdrukken

Met de volgende code worden de overeenkomende gegevens op de console afgedrukt:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_find_similar_print)]

## <a name="create-and-train-a-person-group"></a>Een personengroep maken en trainen

Met de volgende code wordt een **persoonsgroep gemaakt** met zes verschillende **persoonsobjecten.** Het associeert elke **persoon** met een reeks voorbeeldbeelden, en dan traint het om elke persoon te herkennen aan hun gezichtskenmerken. **Persoons-** en **persoonsgroepobjecten** worden gebruikt in de bewerkingen Verifiëren, Identificeren en Groeperen.

Als u dit nog niet hebt gedaan, definieert u de volgende URL-tekenreeks aan de hoofdtekst van uw klas. Dit wijst op een reeks voorbeeldafbeeldingen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_image_url)]

De code later in deze sectie geeft een herkenningsmodel op om gegevens uit gezichten te extraheren en in het volgende fragment worden verwijzingen naar de beschikbare modellen gemaakt. Zie [Een herkenningsmodel opgeven](../Face-API-How-to-Topics/specify-recognition-model.md) voor informatie over herkenningsmodellen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_models)]

### <a name="create-persongroup"></a>Persoonsgroep maken

Declareer een tekenreeksvariabele aan de hoofdmap van uw klas om de id van de **persoonsgroep weer** te geven die u maakt.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_declare)]

Voeg in een nieuwe methode de volgende code toe. Deze code koppelt de namen van personen aan hun voorbeeldafbeeldingen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_files)]

Voeg vervolgens de volgende code toe om voor elke persoon in het woordenboek een **persoon** te maken en de gezichtsgegevens van de juiste afbeeldingen toe te voegen. Elk **object persoon** is aan dezelfde **persoonsgroep** gekoppeld via de unieke id-tekenreeks. Vergeet niet om `client`de `url`variabelen `RECOGNITION_MODEL1` door te geven, en in deze methode.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_create)]

### <a name="train-persongroup"></a>Trein persoonsgroep

Zodra u gezichtsgegevens uit uw afbeeldingen hebt geëxtraheerd en deze in verschillende **persoonsobjecten** hebt gesorteerd, moet u de **persoonsgroep** trainen om de visuele functies te identificeren die zijn gekoppeld aan elk van de **persoonsobjecten.** De volgende code roept de asynchrone **treinmethode** aan en peilt de resultaten en drukt de status af op de console.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_train)]

Deze **groep Persoon** en de bijbehorende **persoonsobjecten** zijn nu klaar om te worden gebruikt in de bewerkingen Verifiëren, Identificeren of Groeperen.

## <a name="identify-a-face"></a>Een gezicht identificeren

Met de bewerking Identificeren wordt een afbeelding gemaakt van een persoon (of meerdere personen) en wordt de identiteit van elk gezicht in de afbeelding gevonden. Het vergelijkt elk gedetecteerd gezicht met een **PersonGroup**, een database van verschillende **persoonobjecten** waarvan de gelaatstrekken bekend zijn.

> [!IMPORTANT]
> Als u dit voorbeeld wilt uitvoeren, moet u eerst de code uitvoeren in [Een personengroep maken en trainen.](#create-and-train-a-person-group) De variabelen die in&mdash;`client` `url`die `RECOGNITION_MODEL1` &mdash;sectie worden gebruikt, en moeten ook hier beschikbaar zijn.

### <a name="get-a-test-image"></a>Een testafbeelding krijgen

Merk op dat de code voor [Een persoonsgroep maken en trainen](#create-and-train-a-person-group) een variabele `sourceImageFileName`definieert. Deze variabele komt overeen&mdash;met de bronafbeelding die de afbeelding bevat die mensen bevat om te identificeren.

### <a name="identify-faces"></a>Gezichten identificeren

De volgende code neemt de bronafbeelding en maakt een lijst met alle gezichten die in de afbeelding zijn gedetecteerd. Dit zijn de gezichten die worden geïdentificeerd tegen de **Persoonsgroep**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_identify_sources)]

In het volgende codefragment wordt de bewerking Identificeren aanroept en worden de resultaten naar de console afgedrukt. Hier probeert de service elk gezicht van de bronafbeelding aan een **persoon** in de opgegeven **persoonsgroep**te koppelen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_identify)]

## <a name="take-a-snapshot-for-data-migration"></a>Een momentopname maken voor gegevensmigratie

Met de functie Momentopnamen u uw opgeslagen Gezichtsgegevens, zoals een getrainde **Persoonsgroep,** verplaatsen naar een ander Azure Cognitive Services Face-abonnement. U deze functie gebruiken als u bijvoorbeeld een **PersonGroup-object** hebt gemaakt met een gratis proefabonnement en het wilt migreren naar een betaald abonnement. Zie [Uw gezichtsgegevens migreren](../Face-API-How-to-Topics/how-to-migrate-face-data.md) voor een overzicht van de functie Momentopnamen.

In dit voorbeeld migreert u de **persoonsgroep die** u hebt gemaakt in [Een personengroep maken en trainen.](#create-and-train-a-person-group) U die sectie eerst voltooien of uw eigen Face-gegevensconstruct(s) maken om te migreren.

### <a name="set-up-target-subscription"></a>Doelabonnement instellen

Ten eerste moet u een tweede Azure-abonnement hebben met een Face-bron. u dit doen door de stappen in de sectie [Instellen te](#setting-up) volgen. 

Definieer vervolgens de volgende variabelen `Main` in de methode van uw programma. U moet nieuwe omgevingsvariabelen maken voor de abonnements-id van uw Azure-account, evenals de sleutel-, eindpunt- en abonnements-id van uw nieuwe (doel)account. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_vars)]

Declareer in dit voorbeeld een variabele voor de ID van de **doelgroepGroep**&mdash;het object dat bij het nieuwe abonnement hoort, waarnaar u uw gegevens kopieert.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_vars)]

### <a name="authenticate-target-client"></a>Doelclient verifiëren

Voeg vervolgens de code toe om uw secundaire Face-abonnement te verifiëren.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_client)]

### <a name="use-a-snapshot"></a>Een momentopname gebruiken

De rest van de momentopnamebewerkingen moet plaatsvinden binnen een asynchrone methode. 

1. De eerste stap is om de momentopname te **maken,** die de gezichtsgegevens van uw oorspronkelijke abonnement opslaat op een tijdelijke cloudlocatie. Met deze methode wordt een id geretourneerd die u gebruikt om de status van de bewerking op te vragen.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_take)]

1. Vraag vervolgens de id op totdat de bewerking is voltooid.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_take_wait)]

1. Gebruik vervolgens de **toepassingsbewerking** om uw gezichtsgegevens naar uw doelabonnement te schrijven. Met deze methode wordt ook een ID-waarde geretourneerd.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_apply)]

1. Queryer nogmaals de nieuwe id totdat de bewerking is voltooid.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_apply)]

1. Tot slot, voltooi de try / catch blok en afwerking van de methode.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_trycatch)]

Op dit moment moet uw nieuwe **PersonGroup-object** dezelfde gegevens hebben als het oorspronkelijke object en toegankelijk zijn vanaf uw nieuwe (doel)Azure Face-abonnement.

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit uw `dotnet run` toepassingsmap uit met de opdracht.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een abonnement voor Cognitive Services wilt opschonen en verwijderen, u de bron- of brongroep verwijderen. Als u de brongroep verwijdert, worden ook andere bronnen verwijderd die eraan zijn gekoppeld.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Als u in deze quickstart een **persoonsgroep** hebt gemaakt en deze wilt verwijderen, voert u de volgende code in uw programma uit:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_delete)]

Definieer de verwijderingsmethode met de volgende code:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_deletepersongroup)]

Als u gegevens migreert met de functie Momentopname in deze quickstart, moet u bovendien de **persoonsgroep** verwijderen die is opgeslagen in het doelabonnement.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_target_persongroup_delete)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u de Face-bibliotheek voor .NET gebruiken om basistaken uit te voeren. Bekijk vervolgens de referentiedocumentatie voor meer informatie over de bibliotheek.

> [!div class="nextstepaction"]
> [Verwijzing naar Face API (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)

* [Wat is de Face-service?](../overview.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub.](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/Face/Program.cs)
