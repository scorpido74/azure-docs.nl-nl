---
title: Snelstartgids voor gezichts ga-client bibliotheek
description: Ga aan de slag met de face-client bibliotheek voor go.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: include
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 4a96f0e887bb04aea6d451e08bd5d26d1cc6edca
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82587876"
---
Ga aan de slag met de face-client bibliotheek voor go. Volg deze stappen om de bibliotheek te installeren en onze voor beelden voor basis taken uit te proberen. De face-service biedt u toegang tot geavanceerde algoritmen voor het detecteren en herkennen van menselijke gezichten in afbeeldingen.

Gebruik de client bibliotheek van Face service voor Ga naar:

* [Gezichten in een afbeelding detecteren](#detect-faces-in-an-image)
* [Vergelijkbare gezichten zoeken](#find-similar-faces)
* [Een persoons groep maken en trainen](#create-and-train-a-person-group)
* [Een gezicht identificeren](#identify-a-face)
* [Een moment opname maken voor gegevens migratie](#take-a-snapshot-for-data-migration)

[Referentie documentatie](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)  |  [Bron code](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face)  |  van bibliotheek [SDK downloaden](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De nieuwste versie van [Go](https://golang.org/dl/)

## <a name="set-up"></a>Instellen

### <a name="create-a-face-azure-resource"></a>Een gezichts-Azure-resource maken 

Begin met het gebruik van de service Face door een Azure-resource te maken. Kies het resource type dat het meest geschikt is voor u:

* Een [proef resource](https://azure.microsoft.com/try/cognitive-services/#decision) (geen Azure-abonnement vereist): 
    * Gratis geldig voor zeven dagen. Nadat u zich hebt aangemeld, is een proef sleutel en een eind punt beschikbaar op de [Azure-website](https://azure.microsoft.com/try/cognitive-services/my-apis/). 
    * Dit is een uitstekende optie als u een gezichts service wilt proberen, maar geen Azure-abonnement hebt.
* Een [Face Service-Resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace):
    * Beschikbaar via de Azure Portal totdat u de resource verwijdert.
    * Gebruik de gratis prijs categorie om de service te proberen en pas later bij te werken naar een betaalde laag voor productie.
* Een [resource met meerdere services](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne):
    * Beschikbaar via de Azure Portal totdat u de resource verwijdert.  
    * Gebruik dezelfde sleutel en hetzelfde eind punt voor uw toepassingen, op meerdere Cognitive Services.

### <a name="create-an-environment-variable"></a>Een omgevings variabele maken

>[!NOTE]
> De eind punten voor niet-proef resources die zijn gemaakt na 1 juli 2019, gebruiken de aangepaste indeling voor subdomeinen die hieronder wordt weer gegeven. Zie [aangepaste subdomein namen voor Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)voor meer informatie en een volledige lijst met regionale eind punten. 

Met uw sleutel en eind punt van de resource die u hebt gemaakt, maakt u twee omgevings variabelen voor verificatie:
* `FACE_SUBSCRIPTION_KEY`-De bron sleutel voor het verifiëren van uw aanvragen.
* `FACE_ENDPOINT`-Het resource-eind punt voor het verzenden van API-aanvragen. Dit ziet er als volgt uit: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Volg de instructies voor uw besturings systeem.
<!-- replace the below endpoint and key examples -->
#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx FACE_SUBSCRIPTION_KEY <replace-with-your-product-name-key>
setx FACE_ENDPOINT <replace-with-your-product-name-endpoint>
```

Nadat u de omgevings variabele hebt toegevoegd, start u het console venster opnieuw.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export FACE_SUBSCRIPTION_KEY=<replace-with-your-product-name-key>
export FACE_ENDPOINT=<replace-with-your-product-name-endpoint>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source ~/.bashrc` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.

#### <a name="macos"></a>[MacOS](#tab/unix)

Bewerk uw `.bash_profile` en voeg de omgevings variabele toe:

```bash
export FACE_SUBSCRIPTION_KEY=<replace-with-your-product-name-key>
export FACE_ENDPOINT=<replace-with-your-product-name-endpoint>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source .bash_profile` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.
***

### <a name="create-a-go-project-directory"></a>Een go-projectmap maken

Maak in een console venster (cmd, Power shell, Terminal, bash) een nieuwe werk ruimte voor uw Go-project `my-app` met de naam en navigeer ernaar.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Uw werk ruimte bevat drie mappen:

* **src** -deze map bevat bron code en pakketten. Alle pakketten die met de `go get` opdracht zijn geïnstalleerd, bevindt zich in deze map.
* **pakket** -deze map bevat de gecompileerde go package-objecten. Deze bestanden hebben allemaal een `.a` uitbrei ding.
* **bin** : deze map bevat de binaire uitvoer bare bestanden die worden gemaakt wanneer u uitvoert `go install` .

> [!TIP]
> Zie de [Go-taal documentatie](https://golang.org/doc/code.html#Workspaces)voor meer informatie over de structuur van een go-werk ruimte. Deze hand leiding bevat informatie over het instellen van `$GOPATH` en `$GOROOT` .

### <a name="install-the-client-library-for-go"></a>De client bibliotheek voor Go installeren

Installeer vervolgens de client bibliotheek voor Go:

```bash
go get -u github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

of, als u dep gebruikt, binnen de uitvoer van de opslagplaats:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

### <a name="create-a-go-application"></a>Een go-toepassing maken

Maak vervolgens een bestand in de **src** -map met de naam `sample-app.go` :

```bash
cd src
touch sample-app.go
```

Open `sample-app.go` de IDE-of tekst editor van uw voor keur. Voeg vervolgens de naam van het pakket toe en importeer de volgende bibliotheken:

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_imports)]

Vervolgens begint u met het toevoegen van code voor het uitvoeren van verschillende gezichts service bewerkingen.

## <a name="object-model"></a>Object model

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de face service go-client bibliotheek.

|Naam|Beschrijving|
|---|---|
|[BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#BaseClient) | Deze klasse vertegenwoordigt uw autorisatie voor het gebruik van de face-service en u hebt deze nodig voor alle gezichts functionaliteit. U maakt de app met uw abonnements gegevens en gebruikt deze om instanties van andere klassen te maken. |
|[Client](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)|Deze klasse verwerkt de basis taken voor detectie en herkenning die u met menselijke gezichten kunt uitvoeren. |
|[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)|Deze klasse vertegenwoordigt alle gegevens die zijn gedetecteerd van één gezicht in een afbeelding. U kunt deze gebruiken om gedetailleerde informatie over het gezicht op te halen.|
|[ListClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#ListClient)|Deze klasse beheert de in de cloud opgeslagen **FaceList** -constructies, waarin een geassorteerde set gezichten wordt opgeslagen. |
|[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)| Deze klasse beheert de door de cloud opgeslagen **persoons** constructies, waarin een set gezichten wordt opgeslagen die deel uitmaakt van één persoon.|
|[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)| Deze klasse beheert de in de cloud opgeslagen **PersonGroup** -constructies, die een set geassorteerde **persoons** objecten opslaan. |
|[SnapshotClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient)|Deze klasse beheert de functionaliteit van de moment opname. U kunt deze gebruiken om al uw op de cloud gebaseerde gezichts gegevens tijdelijk op te slaan en deze gegevens te migreren naar een nieuw Azure-abonnement. |

## <a name="code-examples"></a>Codevoorbeelden

Deze code voorbeelden laten zien hoe u basis taken kunt uitvoeren met behulp van de client bibliotheek voor de face-service voor Go:

* [De client verifiëren](#authenticate-the-client)
* [Gezichten in een afbeelding detecteren](#detect-faces-in-an-image)
* [Vergelijkbare gezichten zoeken](#find-similar-faces)
* [Een persoons groep maken en trainen](#create-and-train-a-person-group)
* [Een gezicht identificeren](#identify-a-face)
* [Een moment opname maken voor gegevens migratie](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE] 
> In deze Quick Start wordt ervan uitgegaan dat u [omgevings variabelen hebt gemaakt](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor uw gezichts sleutel en eind punt, met de naam `FACE_SUBSCRIPTION_KEY` en `FACE_ENDPOINT` respectievelijk.

Maak een **hoofd** functie en voeg de volgende code toe om een client met uw eind punt en sleutel te instantiëren. U maakt een **[CognitiveServicesAuthorizer](https://godoc.org/github.com/Azure/go-autorest/autorest#CognitiveServicesAuthorizer)** -object met uw sleutel en gebruikt dit met uw eind punt om een **[client](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)** object te maken. Deze code maakt ook een instantie van een context object, dat nodig is voor het maken van client-objecten. Er wordt ook een externe locatie gedefinieerd waar enkele van de voorbeeld afbeeldingen in deze Snelstartgids worden gevonden.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_main_client)]


## <a name="detect-faces-in-an-image"></a>Gezichten in een afbeelding detecteren

Voeg de volgende code toe aan de methode **Main** . Met deze code wordt een externe voorbeeld afbeelding gedefinieerd en wordt aangegeven welke gezichts functies moeten worden geëxtraheerd uit de installatie kopie. Er wordt ook aangegeven welk AI-model moet worden gebruikt om gegevens op te halen uit de gedetecteerde gezicht (en). Zie [een herkennings model opgeven](../../Face-API-How-to-Topics/specify-recognition-model.md) voor meer informatie over deze opties. Ten slotte wordt de methode **[DetectWithURL](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.DetectWithURL)** de detectie bewerking voor het gezichts proces op de installatie kopie uitgevoerd en worden de resultaten opgeslagen in het programma geheugen.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Gedetecteerde gezichts gegevens weer geven

In het volgende code blok wordt het eerste element in de matrix met **[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)** -objecten gebruikt en worden de kenmerken ervan naar de console afgedrukt. Als u een afbeelding met meerdere gezichten hebt gebruikt, moet u in plaats daarvan de matrix herhalen.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect_display)]

## <a name="find-similar-faces"></a>Vergelijkbare gezichten zoeken

De volgende code neemt één gedetecteerd gezicht (bron) en doorzoekt een set andere gezichten (doel) om overeenkomsten te vinden. Wanneer er een overeenkomst wordt gevonden, wordt de ID van het overeenkomende gezicht afgedrukt op de console.

### <a name="detect-faces-for-comparison"></a>Gezichten detecteren voor vergelijking

Sla eerst een verwijzing op naar het gezicht dat u hebt gedetecteerd in de sectie [gezichten detecteren in een installatie kopie](#detect-faces-in-an-image) . Dit gezicht is de bron.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_single_ref)]

Voer vervolgens de volgende code in om een set gezichten in een andere afbeelding te detecteren. Deze gezichten zijn het doel.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_multiple_ref)]

### <a name="find-matches"></a>Overeenkomsten zoeken

In de volgende code wordt de methode **[FindSimilar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.FindSimilar)** gebruikt om alle doel gezichten te vinden die overeenkomen met het bron vlak.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar)]

### <a name="print-matches"></a>Overeenkomsten afdrukken

Met de volgende code worden de overeenkomende gegevens in de-console afgedrukt.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_print)]


## <a name="create-and-train-a-person-group"></a>Een persoons groep maken en trainen

Als u dit scenario wilt door lopen, moet u de volgende installatie kopieën opslaan in de hoofd directory van uw project: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images .

Deze groep met installatie kopieën bevat drie sets installatie kopieën met één gezicht die overeenkomen met drie verschillende personen. Met de code worden drie **PersonGroup person** -objecten gedefinieerd en gekoppeld aan afbeeldings bestanden die beginnen met `woman` , `man` en `child` .

### <a name="create-persongroup"></a>PersonGroup maken

Nadat u uw installatie kopieën hebt gedownload, voegt u de volgende code toe aan de onderkant van de methode **Main** . Met deze code wordt een **[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)** -object geverifieerd en vervolgens gebruikt om een nieuwe **PersonGroup**te definiëren.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_setup)]

### <a name="create-persongroup-persons"></a>PersonGroup personen maken

In het volgende code blok wordt een **[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)** geverifieerd en gebruikt om drie nieuwe PersonGroup- **personen** objecten te definiëren. Deze objecten vertegenwoordigen elk één persoon in de set met installatie kopieën.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_setup)]

### <a name="assign-faces-to-persons"></a>Gezichten toewijzen aan personen

Met de volgende code worden de afbeeldingen gesorteerd op basis van het voor voegsel, worden gezichten gedetecteerd en worden de gezichten toegewezen aan elk **PersonGroup persoons** object, gebaseerd op de naam van het afbeeldings bestand.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_assign)]

### <a name="train-persongroup"></a>PersonGroup trainen

Zodra u gezichten hebt toegewezen, traint u de **PersonGroup** zodat deze de visuele functies kan identificeren die aan elk van zijn **persoons** objecten zijn gekoppeld. Met de volgende code wordt de asynchrone **trein** methode aangeroepen en wordt het resultaat gecontroleerd en wordt de status afgedrukt naar de-console.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_train)]

## <a name="identify-a-face"></a>Een gezicht identificeren

De volgende code neemt een afbeelding met meerdere gezichten en zoekt naar de identiteit van elke persoon in de installatie kopie. Elk gedetecteerd gezicht wordt vergeleken met een **PersonGroup**, een Data Base van verschillende **personen** -objecten waarvan de gezichts kenmerken bekend zijn.

> [!IMPORTANT]
> Als u dit voor beeld wilt uitvoeren, moet u eerst de code uitvoeren in [een persoons groep maken en trainen](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Een test installatie kopie ophalen

De volgende code zoekt in de hoofdmap van het project naar een afbeelding _Test-Image-person-Group. jpg_ en laadt deze in het programma geheugen. U kunt deze installatie kopie vinden in dezelfde opslag plaats als de installatie kopieën die worden gebruikt voor het [maken en trainen van een persoons groep](#create-and-train-a-person-group): https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images .

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_get)]

### <a name="detect-source-faces-in-test-image"></a>Bron gezichten in test installatie kopie detecteren

In het volgende code blok wordt normaal gezichts detectie op de test installatie kopie gebruikt om alle gezichten op te halen en deze op te slaan in een matrix.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_detect)]

### <a name="identify-faces"></a>Gezichten identificeren

De methode **[Identify](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.Identify)** gebruikt de matrix met gedetecteerde gezichten en vergelijkt deze met de opgegeven **PersonGroup** (gedefinieerd en getraind in de vorige sectie). Als de app een gedetecteerd gezicht kan overeenkomen met een **persoon** in de groep, wordt het resultaat opgeslagen.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id)]

Met deze code worden vervolgens gedetailleerde resultaten van de overeenkomst naar de console afgedrukt.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_print)]


## <a name="verify-faces"></a>Gezichten verifiëren

Bij de controle bewerking wordt een face-ID en een andere gezichts-ID of een **persoons** object gebruikt en wordt bepaald of deze deel uitmaken van dezelfde persoon.

De volgende code detecteert gezichten in twee bron afbeeldingen en verifieert deze vervolgens met een gezicht dat is gedetecteerd vanuit een doel installatie kopie.

### <a name="get-test-images"></a>Test installatie kopieën ophalen

De volgende code blokkeert variabelen die verwijzen naar de doel-en bron installatie kopieën voor de verificatie bewerking.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_images)]

### <a name="detect-faces-for-verification"></a>Gezichten voor verificatie detecteren

De volgende code detecteert gezichten in de bron-en doel installatie kopieën en slaat ze op in variabelen.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_source)]

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_target)]

### <a name="get-verification-results"></a>Resultaten van de verificatie ophalen

De volgende code vergelijkt elk van de bron installatie kopieën met de doel installatie kopie en drukt een bericht af dat aangeeft of ze tot dezelfde persoon behoren.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver)]


## <a name="take-a-snapshot-for-data-migration"></a>Een moment opname maken voor gegevens migratie

Met de functie moment opnamen kunt u opgeslagen gezichts gegevens, zoals een getraind **PersonGroup**, verplaatsen naar een ander Azure Cognitive Services Face-abonnement. U kunt deze functie gebruiken als u bijvoorbeeld een **PersonGroup** -object hebt gemaakt met een gratis proef abonnement en nu wilt migreren naar een betaald abonnement. Zie de [informatie over uw gezicht migreren](../../Face-API-How-to-Topics/how-to-migrate-face-data.md) voor een uitgebreid overzicht van de functie moment opnamen.

In dit voor beeld migreert u de **PersonGroup** die u hebt gemaakt in [een persoons groep maken en trainen](#create-and-train-a-person-group). U kunt deze sectie eerst volt ooien of uw eigen face data-construct (s) gebruiken.

### <a name="set-up-target-subscription"></a>Doel abonnement instellen

Eerst moet u een tweede Azure-abonnement hebben met een face-resource. u kunt dit doen door de stappen in de sectie [instellen](#set-up) te herhalen. 

Maak vervolgens de volgende variabelen aan de bovenkant van de methode **Main** . U moet ook nieuwe omgevings variabelen maken voor de abonnements-ID van uw Azure-account, evenals de sleutel, het eind punt en de abonnements-ID van uw nieuwe (doel) account.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_target_client)]

Plaats vervolgens uw abonnements-ID-waarde in een matrix voor de volgende stappen.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_id)]

### <a name="authenticate-target-client"></a>Doel-client verifiëren

Sla het oorspronkelijke client object later in uw script op als de bron-client en Verifieer vervolgens een nieuw client object voor uw doel abonnement. 

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_auth)]

### <a name="take-a-snapshot"></a>Een momentopname maken

De volgende stap bestaat uit het **[nemen van de moment opname, waarbij](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Take)** u de gezichts gegevens van uw oorspronkelijke abonnement opslaat op een tijdelijke locatie van de Cloud. Deze methode retourneert een ID die u gebruikt om de status van de bewerking op te vragen.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_take)]

Vervolgens moet u een query uitvoeren op de ID totdat de bewerking is voltooid.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_query)]

### <a name="apply-the-snapshot"></a>De moment opname Toep assen

Gebruik de bewerking **[Apply](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Apply)** om uw zojuist geüploade gezichts gegevens te schrijven naar uw doel abonnement. Met deze methode wordt ook een ID geretourneerd.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply)]

Daarna moet u een query uitvoeren op de ID totdat de bewerking is voltooid.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply_query)]

Zodra u deze stappen hebt voltooid, kunt u toegang krijgen tot uw gezichts gegevens constructies vanuit uw nieuwe (doel) abonnement.

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer uw Go-toepassing uit met de `go run [arguments]` opdracht uit de toepassingsmap.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Als u in deze Quick Start een **PersonGroup** hebt gemaakt en u deze wilt verwijderen, roept u de **[Delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient.Delete)** -methode aan. Als u gegevens hebt gemigreerd met behulp van de snap shot-functie in deze Quick Start, moet u ook de **PersonGroup** verwijderen die is opgeslagen in het doel abonnement.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u de face-bibliotheek kunt gebruiken voor basis taken van go. Bekijk vervolgens de referentie documentatie voor meer informatie over de-bibliotheek.

> [!div class="nextstepaction"]
> [Face-API referentie (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)

* [Wat is de Face-service?](../../overview.md)
* De bron code voor dit voor beeld is te vinden op [github](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/Face/FaceQuickstart.go).
