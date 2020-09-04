---
title: Quickstart voor Face Go-clientbibliotheek
description: Ga aan de slag met de Face-clientbibliotheek voor Go.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: include
ms.date: 08/17/2020
ms.author: pafarley
ms.openlocfilehash: bb8f80baac5ce49db612a7d3a599d727b824bffc
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505861"
---
Ga aan de slag met de Face-clientbibliotheek voor Go. Volg deze stappen om de bibliotheek te installeren en onze voorbeelden voor basistaken uit te proberen. De Face-service biedt u toegang tot geavanceerde algoritmen voor het detecteren en herkennen van menselijke gezichten in afbeeldingen.

Gebruik de clientbibliotheek van de Face-service voor Go voor het volgende:

* [Gezichten in een afbeelding detecteren](#detect-faces-in-an-image)
* [Vergelijkbare gezichten zoeken](#find-similar-faces)
* [Een persoonsgroep maken en trainen](#create-and-train-a-person-group)
* [Een gezicht identificeren](#identify-a-face)
* [Een momentopname maken voor gegevensmigratie](#take-a-snapshot-for-data-migration)

[Referentiedocumentatie](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face) | [Broncode bibliotheek](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face) | [SDK download](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Vereisten

* Nieuwste versie van [Go](https://golang.org/dl/)
* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* Zodra u een Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Een Face-resource maken"  target="_blank">maakt u een Face-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in Azure Portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Face-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.
* Nadat u een sleutel en eindpunt hebt verkregen, gaat u [omgevingsvariabelen maken](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de sleutel en het eindpunt, respectievelijk `FACE_SUBSCRIPTION_KEY` en `FACE_ENDPOINT` genaamd.

## <a name="setting-up"></a>Instellen

### <a name="create-a-go-project-directory"></a>Een Go-projectmap maken

Maak in een consolevenster (cmd, PowerShell, Terminal, Bash) een nieuwe werkruimte voor uw Go-project, genaamd `my-app` en navigeer er naartoe.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Uw werkruimte bevat drie mappen:

* **src**: deze map bevat broncode en pakketten. Alle pakketten die zijn geïnstalleerd met de opdracht `go get` bevinden zich in deze map.
* **pkg**: deze map bevat de gecompileerde Go-pakketobjecten. Deze bestanden hebben allemaal een `.a`-extensie.
* **bin**: deze map bevat de binaire uitvoerbare bestanden die worden gemaakt wanneer u `go install` uitvoert.

> [!TIP]
> Zie de [documentatie over de taal Go](https://golang.org/doc/code.html#Workspaces) voor meer informatie over de structuur van een Go-werkruimte. Deze handleiding bevat informatie om `$GOPATH` en `$GOROOT` in te stellen.

### <a name="install-the-client-library-for-go"></a>De clientbibliotheek installeren voor Go

Installeer vervolgens de clientbibliotheek voor Go:

```bash
go get -u github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

of, als u dep gebruikt, binnen de uitvoer van de opslagplaats:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

### <a name="create-a-go-application"></a>Een Go-toepassing maken

Maak vervolgens een bestand in de map **src** met de naam `sample-app.go`:

```bash
cd src
touch sample-app.go
```

Open `sample-app.go` in uw favoriete IDE of teksteditor. Voeg vervolgens de pakketnaam toe en importeer de volgende bibliotheken:

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_imports)]

Vervolgens begint u met het toevoegen van code voor het uitvoeren van verschillende Face-servicebewerkingen.

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Face-service Go-clientbibliotheek.

|Naam|Beschrijving|
|---|---|
|[BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#BaseClient) | Deze klasse vertegenwoordigt uw autorisatie voor het gebruik van de Face-service. U hebt deze nodig voor alle Face-functies. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om instanties van andere klassen te maken. |
|[Client](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)|Deze klasse verwerkt de basistaken voor detectie en herkenning die u met menselijke gezichten kunt uitvoeren. |
|[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)|Deze klasse vertegenwoordigt alle gegevens die zijn gedetecteerd van één gezicht in een afbeelding. U kunt deze gebruiken om gedetailleerde informatie over het gezicht op te halen.|
|[ListClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#ListClient)|Deze klasse beheert de in de cloud opgeslagen **FaceList**-constructies, waarin een geassorteerde set gezichten wordt opgeslagen. |
|[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)| Deze klasse beheert de in de cloud opgeslagen **Person**-constructies, die een set gezichten opslaan die tot één persoon behoren.|
|[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)| Deze klasse beheert de in de cloud opgeslagen **PersonGroup**-constructies, die een set van verschillende **Person**-objecten opslaan. |
|[SnapshotClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient)|Deze klasse beheert de functionaliteit van de momentopname. U kunt deze gebruiken om al uw op de cloud gebaseerde gezichtsgegevens tijdelijk op te slaan en deze gegevens te migreren naar een nieuw Azure-abonnement. |

## <a name="code-examples"></a>Codevoorbeelden

Deze codevoorbeelden laten zien hoe u basistaken kunt uitvoeren met de Face-service-clientbibliotheek voor Go:

* [De client verifiëren](#authenticate-the-client)
* [Gezichten in een afbeelding detecteren](#detect-faces-in-an-image)
* [Vergelijkbare gezichten zoeken](#find-similar-faces)
* [Een persoonsgroep maken en trainen](#create-and-train-a-person-group)
* [Een gezicht identificeren](#identify-a-face)
* [Een momentopname maken voor gegevensmigratie](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE] 
> In deze quickstart wordt ervan uitgegaan dat u [omgevingsvariabelen hebt gemaakt](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor uw Face-sleutel en -eindpunt, met de naam `FACE_SUBSCRIPTION_KEY` en `FACE_ENDPOINT`.

Maak een **Main**-functie en voeg de volgende code toe om een client te instantiëren met uw eindpunt en sleutel. Maak een **[CognitiveServicesAuthorizer](https://godoc.org/github.com/Azure/go-autorest/autorest#CognitiveServicesAuthorizer)** -object met uw sleutel en gebruik het met uw eindpunt om een **[Client](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)** -object te maken. Deze code instantieert ook een contextobject, dat nodig is voor het maken van clientobjecten. Er wordt ook een externe locatie gedefinieerd waar een aantal van de voorbeeldafbeeldingen in deze quickstart worden gevonden.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_main_client)]


## <a name="detect-faces-in-an-image"></a>Gezichten in een afbeelding detecteren

Voeg de volgende code in uw **Main**-methode toe. Met deze code wordt een externe voorbeeldafbeelding gedefinieerd en wordt aangegeven welke gezichtskenmerken moeten worden geëxtraheerd uit de afbeelding. Er wordt ook aangegeven welk AI-model moet worden gebruikt om gegevens op te halen uit de gedetecteerde gezichten. Zie [Een herkenningsmodel opgeven](../../Face-API-How-to-Topics/specify-recognition-model.md) voor meer informatie over deze opties. Ten slotte voert de methode **[DetectWithURL](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.DetectWithURL)** de gezichtsherkenning uit op de afbeelding en worden de resultaten opgeslagen in het programmageheugen.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Gedetecteerde gezichtsgegevens weergeven

In het volgende codeblok wordt het eerste element in de matrix van **[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)** -objecten gebruikt en worden de kenmerken ervan naar de console afgedrukt. Als u een afbeelding met meerdere gezichten hebt gebruikt, moet u in plaats daarvan de matrix herhalen.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect_display)]

## <a name="find-similar-faces"></a>Vergelijkbare gezichten zoeken

De volgende code neemt één gedetecteerd gezicht (bron) en doorzoekt een set andere gezichten (doel) om overeenkomsten te vinden. Wanneer er een overeenkomst wordt gevonden, wordt de id van het overeenkomende gezicht afgedrukt op de console.

### <a name="detect-faces-for-comparison"></a>Gezichten detecteren voor vergelijking

Sla eerst een verwijzing op naar het gezicht dat u hebt gedetecteerd in de sectie [Gezichten detecteren in een afbeelding](#detect-faces-in-an-image). Dit gezicht is de bron.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_single_ref)]

Voer vervolgens de volgende code in om een set gezichten in een andere afbeelding te detecteren. Deze gezichten zijn het doel.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_multiple_ref)]

### <a name="find-matches"></a>Overeenkomsten zoeken

De volgende code maakt gebruik van de methode **[FindSimilar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.FindSimilar)** om alle doelgezichten te vinden die overeenkomen met het brongezicht.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar)]

### <a name="print-matches"></a>Overeenkomsten afdrukken

Met de volgende code worden de overeenkomende resultaten op de console weergegeven.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_print)]


## <a name="create-and-train-a-person-group"></a>Een persoonsgroep maken en trainen

Als u dit scenario wilt doorlopen, moet u de volgende afbeeldingen opslaan in de hoofdmap van uw project: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

Deze groep met afbeeldingen bevat drie sets enkele gezichtsafbeeldingen die overeenkomen met drie verschillende personen. Met de code worden drie **PersonGroup Person**-objecten gedefinieerd en gekoppeld aan afbeeldingsbestanden die beginnen met `woman`, `man` en `child`.

### <a name="create-persongroup"></a>PersonGroup maken

Nadat u uw afbeeldingen hebt gedownload, voegt u de volgende code toe achter de **Main**-methode. Met deze code wordt een **[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)** -object geverifieerd en vervolgens gebruikt om een nieuwe **PersonGroup** te definiëren.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_setup)]

### <a name="create-persongroup-persons"></a>PersonGroup-personen maken

Het volgende codeblok verifieert een **[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)** en gebruikt deze om drie nieuwe **PersonGroup Person**-objecten te definiëren. Deze objecten vertegenwoordigen elk één persoon in de reeks afbeeldingen.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_setup)]

### <a name="assign-faces-to-persons"></a>Gezichten toewijzen aan personen

Met de volgende code worden de afbeeldingen gesorteerd op basis van het voorvoegsel, worden de gezichten gedetecteerd en worden de gezichten toegewezen aan elk **PersonGroup Person**-object, gebaseerd op de naam van het afbeeldingsbestand.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_assign)]

### <a name="train-persongroup"></a>PersonGroup trainen

Zodra u gezichten hebt toegewezen, moet u de **PersonGroup** trainen zodat deze de visuele functies kan identificeren die zijn gekoppeld aan elk van de **Person**-objecten. Met de volgende code wordt de asynchrone **Train**-methode aangeroepen en worden de resultaten gecontroleerd, en wordt de status naar de console afgedrukt.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_train)]

## <a name="identify-a-face"></a>Een gezicht identificeren

De volgende code neemt een afbeelding met meerdere gezichten en zoekt naar de identiteit van elke persoon in de afbeelding. Elk gedetecteerd gezicht wordt vergeleken met een **PersonGroup**, een database van verschillende **Person**-objecten waarvan de gezichtskenmerken bekend zijn.

> [!IMPORTANT]
> Als u dit voorbeeld wilt uitvoeren, moet u eerst de code uitvoeren in [Een persoonsgroep maken en trainen](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Een testafbeelding ophalen

De volgende code zoekt in de hoofdmap van uw project naar een afbeelding _test-image-person-group. jpg_ en laadt deze in het programmageheugen. U vindt deze afbeelding in dezelfde opslagplaats als de afbeeldingen die worden gebruikt in [Een persoonsgroep maken en trainen](#create-and-train-a-person-group): https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_get)]

### <a name="detect-source-faces-in-test-image"></a>Brongezichten in testafbeelding detecteren

In het volgende codeblok wordt normale gezichtsdetectie op de testafbeelding uitgevoerd om alle gezichten op te halen en op te slaan in een matrix.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_detect)]

### <a name="identify-faces"></a>Gezichten identificeren

De methode **[Identificeren](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.Identify)** neemt de matrix van gedetecteerde gezichten en vergelijkt deze met de opgegeven **PersonGroup** (gedefinieerd en getraind in de eerdere sectie). Als een gedetecteerd gezicht overeenkomt met een **Person** in de groep, wordt het resultaat opgeslagen.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id)]

Met deze code worden gedetailleerde overeenkomende resultaten naar de console afgedrukt.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_print)]


## <a name="verify-faces"></a>Gezichten verifiëren

De bewerking Verifiëren neemt een gezichts-id en een andere gezichts-id of een **Person**-object en bepaalt of deze van dezelfde persoon zijn.

De volgende code detecteert gezichten in twee bronafbeeldingen en vergelijkt deze vervolgens met een gezicht dat is gedetecteerd in een doelafbeelding.

### <a name="get-test-images"></a>Testafbeeldingen ophalen

De volgende codeblokken declareren variabelen die verwijzen naar de bron- en doelafbeeldingen voor de verificatiebewerking.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_images)]

### <a name="detect-faces-for-verification"></a>Gezichten voor verificatie detecteren

De volgende code detecteert gezichten in de bron- en doelafbeeldingen en slaat ze op in variabelen.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_source)]

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_target)]

### <a name="get-verification-results"></a>Verificatieresultaten ophalen

De volgende code vergelijkt elk van de bronafbeeldingen met de doelafbeelding en drukt een bericht af dat aangeeft of ze van dezelfde persoon zijn.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver)]


## <a name="take-a-snapshot-for-data-migration"></a>Een momentopname maken voor gegevensmigratie

Met de functie Momentopnamen kunt u opgeslagen gezichtsgegevens, zoals een getrainde **PersonGroup**, verplaatsen naar een ander Azure Cognitive Services Face-abonnement. U kunt deze functie gebruiken als u bijvoorbeeld een **PersonGroup**-object hebt gemaakt met behulp van een gratis abonnement en wilt migreren naar een betaald abonnement. Zie [Uw gezichtsgegevens migreren](../../Face-API-How-to-Topics/how-to-migrate-face-data.md) voor een overzicht van de functie momentopnamen.

In dit voorbeeld migreert u de **PersonGroup** die u hebt gemaakt in [Een groep personen maken en trainen](#create-and-train-a-person-group). U kunt deze sectie eerst voltooien of uw eigen Face-gegevensconstructies gebruiken.

### <a name="set-up-target-subscription"></a>Doelabonnement instellen

Eerst moet u een tweede Azure-abonnement hebben met een Face-resource. U kunt dit doen door de stappen te herhalen in het gedeelte [Instellen](#setting-up). 

Maak vervolgens de volgende variabelen bovenaan uw **Main**-methode. U moet nieuwe omgevingsvariabelen maken voor de abonnements-id van uw Azure-account, evenals de sleutel-, eindpunt- en abonnements-id van uw nieuwe (doel)account.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_target_client)]

Plaats vervolgens uw abonnements-id-waarde in een matrix voor de volgende stappen.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_id)]

### <a name="authenticate-target-client"></a>Doelclient verifiëren

Sla later in uw script uw oorspronkelijke clientobject op als bronclient en verifieer vervolgens een nieuw clientobject voor uw doelabonnement. 

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_auth)]

### <a name="take-a-snapshot"></a>Een momentopname maken

De volgende stap is het nemen van de momentopname met **[Nemen](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Take)** , waarmee de gezichtsgegevens van uw oorspronkelijke abonnement worden opgeslagen op een tijdelijke locatie van de cloud. Deze methode retourneert een id die u gebruikt om de status van de bewerking op te vragen.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_take)]

Vervolgens moet u een query uitvoeren op de id totdat de bewerking is voltooid.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_query)]

### <a name="apply-the-snapshot"></a>De momentopname toepassen

Gebruik vervolgens de bewerking **[Toepassen](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Apply)** om uw nieuw geladen gezichtsgegevens te schrijven naar uw doelabonnement. Met deze methode wordt ook een id geretourneerd.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply)]

Vervolgens moet u een query uitvoeren op de id totdat de bewerking is voltooid.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply_query)]

Zodra u deze stappen hebt voltooid, hebt u toegang tot uw gezichtsgegevens van uw nieuwe (doel)abonnement.

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer uw Go-toepassing uit vanuit uw toepassingsmap met de opdracht `go run [arguments]`.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Als u in deze quickstart een **PersonGroup** hebt gemaakt en u deze wilt verwijderen, roept u de volgende methode voor **[Verwijderen](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient.Delete)** aan. Als u gegevens hebt gemigreerd met behulp van de momentopnamefunctie in deze quickstart, moet u ook de **PersonGroup** verwijderen die is opgeslagen in het doelabonnement.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u de Face-bibliotheek voor .NET kunt gebruiken om basistaken uit te voeren. Bestudeer daarna het naslagmateriaal bij de Face-API voor meer informatie.

> [!div class="nextstepaction"]
> [Verwijzing naar de Face-API (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)

* [Wat is de Face-service?](../../overview.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/Face/FaceQuickstart.go).
