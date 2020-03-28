---
title: 'Snelstart: Face-clientbibliotheek voor Onderweg | Microsoft Documenten'
description: Ga aan de slag met de Face-clientbibliotheek voor Go.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 2db40150167a8f16242b2feb15b77820fa1970a9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76941428"
---
# <a name="quickstart-face-client-library-for-go"></a>Snelstart: Face-clientbibliotheek voor Go

Ga aan de slag met de Face-clientbibliotheek voor Go. Volg deze stappen om de bibliotheek te installeren en probeer onze voorbeelden uit voor basistaken. De Face-service biedt u toegang tot geavanceerde algoritmen voor het detecteren en herkennen van menselijke gezichten in afbeeldingen.

Gebruik de Clientbibliotheek face-service voor Ga naar:

* [Gezichten in een afbeelding detecteren](#detect-faces-in-an-image)
* [Vergelijkbare gezichten zoeken](#find-similar-faces)
* [Een personengroep maken en trainen](#create-and-train-a-person-group)
* [Een gezicht identificeren](#identify-a-face)
* [Een momentopname maken voor gegevensmigratie](#take-a-snapshot-for-data-migration)

[Referentiedocumentatie](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face) | [Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face) | [SDK downloaden](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/)
* De nieuwste versie van [Go](https://golang.org/dl/)

## <a name="set-up"></a>Instellen

### <a name="create-a-face-azure-resource"></a>Een Face Azure-bron maken 

Begin met het gebruik van de Face-service door een Azure-bron te maken. Kies het brontype dat bij u hoort:

* Een [proefbron](https://azure.microsoft.com/try/cognitive-services/#decision) (geen Azure-abonnement nodig): 
    * Geldig voor zeven dagen, gratis. Na het aanmelden zijn een proefsleutel en eindpunt beschikbaar op de [Azure-website.](https://azure.microsoft.com/try/cognitive-services/my-apis/) 
    * Dit is een geweldige optie als u Face-service wilt proberen, maar geen Azure-abonnement hebt.
* Een [Face-servicebron:](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)
    * Beschikbaar via de Azure-portal totdat u de bron verwijdert.
    * Gebruik de gratis prijscategorie om de service uit te proberen en upgrade later naar een betaalde laag voor productie.
* Een [Multi-Service resource:](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)
    * Beschikbaar via de Azure-portal totdat u de bron verwijdert.  
    * Gebruik hetzelfde sleutel- en eindpunt voor uw toepassingen voor meerdere cognitive services.

### <a name="create-an-environment-variable"></a>Een omgevingsvariabele maken

>[!NOTE]
> De eindpunten voor niet-proefresources die na 1 juli 2019 zijn gemaakt, gebruiken de aangepaste subdomeinindeling hieronder. Zie [Aangepaste subdomeinnamen voor cognitieve services voor](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)meer informatie en een volledige lijst met regionale eindpunten. 

Maak met behulp van uw sleutel en eindpunt van de resource die u hebt gemaakt twee omgevingsvariabelen voor verificatie:
* `FACE_SUBSCRIPTION_KEY`- De resourcesleutel voor het verifiëren van uw aanvragen.
* `FACE_ENDPOINT`- Het resourceeindpunt voor het verzenden van API-aanvragen. Het zal er als volgt uitzien: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Gebruik de instructies voor uw besturingssysteem.
<!-- replace the below endpoint and key examples -->
#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx FACE_SUBSCRIPTION_KEY <replace-with-your-product-name-key>
setx FACE_ENDPOINT <replace-with-your-product-name-endpoint>
```

Nadat u de omgevingsvariabele hebt toegevoegd, start u het consolevenster opnieuw.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export FACE_SUBSCRIPTION_KEY=<replace-with-your-product-name-key>
export FACE_ENDPOINT=<replace-with-your-product-name-endpoint>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source ~/.bashrc` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.

#### <a name="macos"></a>[Macos](#tab/unix)

Bewerk `.bash_profile`uw onaantal en voeg de omgevingsvariabele toe:

```bash
export FACE_SUBSCRIPTION_KEY=<replace-with-your-product-name-key>
export FACE_ENDPOINT=<replace-with-your-product-name-endpoint>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source .bash_profile` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.
***

### <a name="create-a-go-project-directory"></a>Een Go-projectmap maken

Maak in een consolevenster (cmd, PowerShell, Terminal, Bash) een `my-app`nieuwe werkruimte voor uw Go-project met de naam en navigeer ernaar.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Uw werkruimte bevat drie mappen:

* **src** - Deze map bevat broncode en pakketten. Alle pakketten die `go get` met de opdracht zijn geïnstalleerd, bevinden zich in deze map.
* **pkg** - Deze directory bevat de gecompileerde Go-pakketobjecten. Deze bestanden hebben `.a` allemaal een extensie.
* **opslaglocatie** - Deze map bevat de binaire uitvoerbare `go install`bestanden die worden gemaakt wanneer u deze uitvoert.

> [!TIP]
> Zie de [taaldocumentatie Go](https://golang.org/doc/code.html#Workspaces)voor meer informatie over de structuur van een Go-werkruimte. Deze handleiding bevat `$GOPATH` informatie `$GOROOT`voor het instellen en .

### <a name="install-the-client-library-for-go"></a>De clientbibliotheek voor Go installeren

Installeer vervolgens de clientbibliotheek voor Go:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

of, als u dep gebruikt, binnen de uitvoer van de opslagplaats:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

### <a name="create-a-go-application"></a>Een Go-toepassing maken

Maak vervolgens een bestand in `sample-app.go`de **src-map** met de naam:

```bash
cd src
touch sample-app.go
```

Open `sample-app.go` in uw favoriete IDE- of teksteditor. Voeg vervolgens de pakketnaam toe en importeer de volgende bibliotheken:

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_imports)]

Vervolgens begin je met het toevoegen van code om verschillende Face-servicebewerkingen uit te voeren.

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces behandelen enkele van de belangrijkste functies van de Face-service Go SDK.

|Name|Beschrijving|
|---|---|
|[Basisclient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#BaseClient) | Deze klasse vertegenwoordigt uw autorisatie om de Face-service te gebruiken en u hebt deze nodig voor alle Face-functionaliteit. U wilt deze met uw abonnementsgegevens en u gebruikt deze om exemplaren van andere klassen te produceren. |
|[Client](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)|Deze klasse behandelt de basisdetectie- en herkenningstaken die u uitvoeren met menselijke gezichten. |
|[GedetecteerdGezicht](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)|Deze klasse vertegenwoordigt alle gegevens die zijn gedetecteerd vanaf één gezicht in een afbeelding. U het gebruiken om gedetailleerde informatie over het gezicht op te halen.|
|[Lijstclient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#ListClient)|Deze klasse beheert de door de cloud opgeslagen **FaceList-constructies,** die een diverse set gezichten opslaan. |
|[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)| Deze klasse beheert de **Person** door de cloud opgeslagen persoonsconstructies, die een reeks gezichten opslaan die van één persoon zijn.|
|[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)| Deze klasse beheert de door de cloud opgeslagen **PersonGroup-constructies,** waarmee een set diverse **persoonsobjecten wordt** opgeslagen. |
|[Momentopnameclient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient)|Deze klasse beheert de snapshot-functionaliteit. U het gebruiken om al uw op de cloud gebaseerde Face-gegevens tijdelijk op te slaan en die gegevens te migreren naar een nieuw Azure-abonnement. |

## <a name="code-examples"></a>Codevoorbeelden

In deze codevoorbeelden ziet u hoe u basistaken uitvoeren met de Clientbibliotheek faceservice voor Onderweg:

* [De client verifiëren](#authenticate-the-client)
* [Gezichten in een afbeelding detecteren](#detect-faces-in-an-image)
* [Vergelijkbare gezichten zoeken](#find-similar-faces)
* [Een personengroep maken en trainen](#create-and-train-a-person-group)
* [Een gezicht identificeren](#identify-a-face)
* [Een momentopname maken voor gegevensmigratie](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE] 
> In deze quickstart wordt ervan uitgegaan dat u [omgevingsvariabelen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) hebt `FACE_SUBSCRIPTION_KEY` `FACE_ENDPOINT` gemaakt voor uw Face-toets en eindpunt, met de naam en respectievelijk.

Maak een **hoofdfunctie** en voeg er de volgende code aan toe om een client te instantiëren met uw eindpunt en sleutel. U maakt een **[CognitiveServicesAuthorizer-object](https://godoc.org/github.com/Azure/go-autorest/autorest#CognitiveServicesAuthorizer)** met uw sleutel en gebruikt het met uw eindpunt om een **[clientobject](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)** te maken. Deze code maakt ook een contextobject, dat nodig is voor het maken van clientobjecten. Het definieert ook een externe locatie waar een aantal van de voorbeeldafbeeldingen in deze quickstart worden gevonden.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_main_client)]


## <a name="detect-faces-in-an-image"></a>Gezichten in een afbeelding detecteren

Voeg de volgende code toe aan de **hoofdmethode.** Deze code definieert een externe voorbeeldafbeelding en geeft aan welke gezichtskenmerken uit de afbeelding moeten worden geëxtraheerd. Het geeft ook aan welk AI-model moet worden gebruikt om gegevens uit het gedetecteerde gezicht(en) te extraheren. Zie [Een herkenningsmodel opgeven](../Face-API-How-to-Topics/specify-recognition-model.md) voor informatie over deze opties. Ten slotte doet de **[DetectWithURL-methode](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.DetectWithURL)** de gezichtsherkenningsbewerking op de afbeelding en slaat de resultaten op in het programmageheugen.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Gedetecteerde gezichtsgegevens weergeven

Het volgende codeblok neemt het eerste element in de array van **[DetectedFace-objecten](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)** en drukt de kenmerken ervan af op de console. Als u een afbeelding met meerdere gezichten hebt gebruikt, moet u in plaats daarvan door de array heen gaan.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect_display)]

## <a name="find-similar-faces"></a>Vergelijkbare gezichten zoeken

De volgende code neemt een enkel gedetecteerd gezicht (bron) en zoekt een set van andere gezichten (doel) om overeenkomsten te vinden. Wanneer het een overeenkomst vindt, drukt het de IDENTITEITSKAART van het gematchte gezicht aan de console af.

### <a name="detect-faces-for-comparison"></a>Gezichten detecteren voor vergelijking

Sla eerst een verwijzing op naar het gezicht dat u hebt gedetecteerd in de [sectie Gezichten detecteren in een afbeeldingssectie.](#detect-faces-in-an-image) Dit gezicht zal de bron zijn.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_single_ref)]

Voer vervolgens de volgende code in om een reeks gezichten in een andere afbeelding te detecteren. Deze gezichten zullen het doelwit zijn.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_multiple_ref)]

### <a name="find-matches"></a>Zoek overeenkomsten

De volgende code gebruikt de **[methode FindSimilar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.FindSimilar)** om alle doelvlakken te vinden die overeenkomen met het brongezicht.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar)]

### <a name="print-matches"></a>Overeenkomen afdrukken

Met de volgende code worden de overeenkomende gegevens op de console afgedrukt.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_print)]


## <a name="create-and-train-a-person-group"></a>Een personengroep maken en trainen

Als u dit scenario wilt doorlopen, moet u de volgende https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/imagesafbeeldingen opslaan in de hoofdmap van uw project: .

Deze groep afbeeldingen bevat drie sets van eenpersoonsafbeeldingen die overeenkomen met drie verschillende personen. De code definieert drie **persongroup person-objecten** en `woman`koppelt `man`deze `child`aan afbeeldingsbestanden die beginnen met , en .

### <a name="create-persongroup"></a>Persoonsgroep maken

Zodra u uw afbeeldingen hebt gedownload, voegt u de volgende code toe aan de onderkant van uw **hoofdmethode.** Deze code verifieert een **[Object PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)** en gebruikt het vervolgens om een nieuwe **persoonsgroep**te definiëren.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_setup)]

### <a name="create-persongroup-persons"></a>Persoonsgroeppersonen maken

Het volgende codeblok verifieert een **[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)** en gebruikt deze om drie nieuwe **persongroupperson-objecten** te definiëren. Deze objecten vertegenwoordigen elk één persoon in de set afbeeldingen.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_setup)]

### <a name="assign-faces-to-persons"></a>Gezichten toewijzen aan personen

In de volgende code worden de afbeeldingen op basis van het voorvoegsel sorteert, worden gezichten gedetecteerd en worden de gezichten aan elk desbetreffend object **PersonGroup Person** toewijst op basis van de naam van het afbeeldingsbestand.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_assign)]

### <a name="train-persongroup"></a>Trein persoonsgroep

Zodra u gezichten hebt toegewezen, traint u de **groep personen** zodat deze de visuele functies kan identificeren die zijn gekoppeld aan elk van de **persoonsobjecten.** De volgende code roept de asynchrone **treinmethode** aan en peilt het resultaat en drukt de status af op de console.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_train)]

## <a name="identify-a-face"></a>Een gezicht identificeren

De volgende code neemt een afbeelding met meerdere gezichten en kijkt naar de identiteit van elke persoon in de afbeelding te vinden. Het vergelijkt elk gedetecteerd gezicht met een **PersonGroup**, een database van verschillende **persoonobjecten** waarvan de gelaatstrekken bekend zijn.

> [!IMPORTANT]
> Als u dit voorbeeld wilt uitvoeren, moet u eerst de code uitvoeren in [Een personengroep maken en trainen.](#create-and-train-a-person-group)

### <a name="get-a-test-image"></a>Een testafbeelding krijgen

De volgende code zoekt in de hoofdmap van uw project naar een _afbeeldingtest-image-person-group.jpg_ en laadt deze in het programmageheugen. U deze afbeelding vinden in dezelfde repo als de afbeeldingen https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/imagesdie worden gebruikt in [Een persoonsgroep maken en trainen:](#create-and-train-a-person-group).

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_get)]

### <a name="detect-source-faces-in-test-image"></a>Bronvlakken detecteren in testafbeelding

Het volgende codeblok doet gewone gezichtsherkenning op de testafbeelding om alle gezichten op te halen en op te slaan in een array.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_detect)]

### <a name="identify-faces"></a>Gezichten identificeren

De methode **[Identificeren](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.Identify)** neemt de array van gedetecteerde gezichten en vergelijkt deze met de opgegeven **persoonsgroep** (gedefinieerd en getraind in de eerdere sectie). Als het een gedetecteerd gezicht kan matchen met een **persoon** in de groep, slaat het het resultaat op.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id)]

Deze code drukt vervolgens gedetailleerde overeenkomende resultaten af op de console.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_print)]


## <a name="verify-faces"></a>Gezichten verifiëren

Met de bewerking Verifiëren wordt een face-id en een ander face-id of een **persoonsobject** gebruikt en wordt bepaald of deze van dezelfde persoon zijn.

De volgende code detecteert gezichten in twee bronafbeeldingen en verifieert vervolgens elk van deze gezichten tegen een gezicht dat is gedetecteerd vanuit een doelafbeelding.

### <a name="get-test-images"></a>Testafbeeldingen bekijken

De volgende codeblokken declareren variabelen die naar het doel en de bronafbeeldingen voor de verificatiebewerking wijzen.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_images)]

### <a name="detect-faces-for-verification"></a>Gezichten detecteren voor verificatie

De volgende code detecteert gezichten in de bron en doelafbeeldingen en slaat deze op variabelen op.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_source)]

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_target)]

### <a name="get-verification-results"></a>Verificatieresultaten behalen

Met de volgende code worden elk van de bronafbeeldingen vergeleken met de doelafbeelding en wordt een bericht afgedrukt dat aangeeft of ze van dezelfde persoon zijn.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver)]


## <a name="take-a-snapshot-for-data-migration"></a>Een momentopname maken voor gegevensmigratie

Met de functie Momentopnamen u uw opgeslagen gezichtsgegevens, zoals een getrainde **persoonsgroep,** verplaatsen naar een ander Azure Cognitive Services Face-abonnement. U deze functie gebruiken als u bijvoorbeeld een **PersonGroup-object** hebt gemaakt met een gratis proefabonnement en het nu wilt migreren naar een betaald abonnement. Zie de [gegevens van Uw gezicht migreren](../Face-API-How-to-Topics/how-to-migrate-face-data.md) voor een breed overzicht van de functie Momentopnamen.

In dit voorbeeld migreert u de **persoonsgroep** die u hebt gemaakt in [Een personengroep maken en trainen.](#create-and-train-a-person-group) U die sectie eerst voltooien of uw eigen Face-gegevensconstructie(s) gebruiken.

### <a name="set-up-target-subscription"></a>Doelabonnement instellen

Ten eerste moet u een tweede Azure-abonnement hebben met een Face-bron. u dit doen door de stappen in de sectie [Instellen te](#set-up) herhalen. 

Maak vervolgens de volgende variabelen boven aan de **hoofdmethode.** U moet ook nieuwe omgevingsvariabelen maken voor de abonnements-id van uw Azure-account, evenals de sleutel-, eindpunt- en abonnements-id van uw nieuwe (doel)account.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_target_client)]

Plaats vervolgens de waarde van uw abonnements-id in een array voor de volgende stappen.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_id)]

### <a name="authenticate-target-client"></a>Doelclient verifiëren

Sla later in uw script het oorspronkelijke clientobject op als bronclient en verifieer vervolgens een nieuw clientobject voor uw doelabonnement. 

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_auth)]

### <a name="take-a-snapshot"></a>Een momentopname maken

De volgende stap is om de momentopname te maken met **[Take](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Take)**, die de gezichtsgegevens van uw oorspronkelijke abonnement opslaat naar een tijdelijke cloudlocatie. Met deze methode wordt een id geretourneerd die u gebruikt om de status van de bewerking op te vragen.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_take)]

Vraag vervolgens de id op totdat de bewerking is voltooid.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_query)]

### <a name="apply-the-snapshot"></a>De momentopname toepassen

Gebruik de bewerking **[Toepassen](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Apply)** om uw nieuw geüploade gezichtsgegevens naar uw doelabonnement te schrijven. Met deze methode wordt ook een ID geretourneerd.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply)]

Queryer en vervolgens opnieuw de id totdat de bewerking is voltooid.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply_query)]

Zodra u deze stappen hebt voltooid, hebt u toegang tot de verzamelingen van gezichtsgegevens van uw nieuwe (doel)abonnement.

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer uw Go-toepassing uit met de `go run [arguments]` opdracht uit uw toepassingsmap.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een abonnement voor Cognitive Services wilt opschonen en verwijderen, u de bron- of brongroep verwijderen. Als u de brongroep verwijdert, worden ook andere bronnen verwijderd die eraan zijn gekoppeld.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Als u in deze quickstart een **persoonsgroep** hebt gemaakt en u deze wilt verwijderen, roept u de methode **[Verwijderen](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient.Delete)** aan. Als u gegevens hebt gemigreerd met de functie Momentopname in deze quickstart, moet u ook de **persoonsgroep** verwijderen die is opgeslagen in het doelabonnement.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u de Face-bibliotheek voor Go gebruiken om basistaken uit te voeren. Bekijk vervolgens de referentiedocumentatie voor meer informatie over de bibliotheek.

> [!div class="nextstepaction"]
> [Verwijzing naar Face API (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)

* [Wat is de Face-service?](../overview.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/Face/FaceQuickstart.go)
