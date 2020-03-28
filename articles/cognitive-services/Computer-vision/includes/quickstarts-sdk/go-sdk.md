---
title: 'Snelstart: Computer Vision-clientbibliotheek voor Go'
titleSuffix: Azure Cognitive Services
description: Ga aan de slag met de Computer Vision-clientbibliotheek voor Gaan met deze quickstart.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: d8f40ab57ee2569b2cb5bf62f391919476b8ab17
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80136012"
---
<a name="HOLTop"></a>

[Broncodepakket naslagdocumentatie](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision) | [Library source code](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision) | [Bibliotheek](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een](https://azure.microsoft.com/free/)
* De nieuwste versie van [Go](https://golang.org/dl/)

## <a name="setting-up"></a>Instellen

### <a name="create-a-computer-vision-azure-resource"></a>Een Azure-bron voor Computer Vision maken

Azure Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een bron voor Computer Vision met de [Azure-portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine. U kunt ook het volgende doen:

* Ontvang een [proefsleutel](https://azure.microsoft.com/try/cognitive-services/#decision) die zeven dagen gratis geldig is. Nadat u zich hebt aangemeld, is deze beschikbaar op de [Azure-website.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Uw bron weergeven op de [Azure-portal.](https://portal.azure.com/)

Nadat u een sleutel uit uw proefabonnement of resource hebt opgehaald, maakt `COMPUTER_VISION_SUBSCRIPTION_KEY` u `COMPUTER_VISION_ENDPOINT` [omgevingsvariabelen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor respectievelijk de naam van de sleutel- en eindpunt-URL.

### <a name="create-a-go-project-directory"></a>Een Go-projectmap maken

Maak in een consolevenster (cmd, PowerShell, Terminal, Bash) een `my-app`nieuwe werkruimte voor uw Go-project met de naam en navigeer ernaar.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Uw werkruimte bevat drie mappen:

* **src** - Deze map bevat broncode en pakketten. Alle pakketten die `go get` met de opdracht zijn geïnstalleerd, worden in deze map weergegeven.
* **pkg** - Deze directory bevat de gecompileerde Go-pakketobjecten. Deze bestanden hebben `.a` allemaal een extensie.
* **opslaglocatie** - Deze map bevat de binaire uitvoerbare `go install`bestanden die worden gemaakt wanneer u deze uitvoert.

> [!TIP]
> Zie de [taaldocumentatie Go](https://golang.org/doc/code.html#Workspaces)voor meer informatie over de structuur van een Go-werkruimte. Deze handleiding bevat `$GOPATH` informatie `$GOROOT`voor het instellen en .

### <a name="install-the-client-library-for-go"></a>De clientbibliotheek voor Go installeren

Installeer vervolgens de clientbibliotheek voor Go:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

of, als u dep gebruikt, binnen de uitvoer van de opslagplaats:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Een Go-toepassing maken

Maak vervolgens een bestand in `sample-app.go`de **src-map** met de naam:

```bash
cd src
touch sample-app.go
```

Open `sample-app.go` in uw favoriete IDE- of teksteditor. Voeg vervolgens de pakketnaam toe en importeer de volgende bibliotheken:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports)]

Declareer ook een context aan de basis van uw script. U hebt dit object nodig om de meeste functieaanroepen van Computer Vision uit te voeren:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_context)]

Vervolgens begin je met het toevoegen van code om verschillende Computer Vision-bewerkingen uit te voeren.

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces behandelen enkele van de belangrijkste functies van de Computer Vision Go SDK.

|Name|Beschrijving|
|---|---|
| [Basisclient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Deze klasse is nodig voor alle Computer Vision-functionaliteit, zoals beeldanalyse en tekstlezen. U wilt deze instantiëren met uw abonnementsgegevens en u gebruikt deze om de meeste beeldbewerkingen uit te voeren.|
|[ImageAnalyse](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ImageAnalysis)| Dit type bevat de resultaten van een **functieaanroep Van AnalyseImage.** Er zijn vergelijkbare typen voor elk van de categoriespecifieke functies.|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| Dit type bevat de resultaten van een batchleesbewerking. |
|[VisualFeatureTypes](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#VisualFeatureTypes)| Dit type definieert de verschillende soorten beeldanalyse die kunnen worden uitgevoerd in een standaardanalysebewerking. U geeft een set VisualFeatureTypes-waarden op, afhankelijk van uw behoeften. |

## <a name="code-examples"></a>Codevoorbeelden

In deze codefragmenten ziet u hoe u de volgende taken uitvoeren met de computervision-clientbibliotheek voor Onderweg:

* [De client verifiëren](#authenticate-the-client)
* [Een afbeelding analyseren](#analyze-an-image)
* [Gedrukte en handgeschreven tekst lezen](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> In deze stap wordt ervan uitgegaan dat u [omgevingsvariabelen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) hebt `COMPUTER_VISION_SUBSCRIPTION_KEY` gemaakt `COMPUTER_VISION_ENDPOINT` voor uw Computer Vision-sleutel en eindpunt, met de naam en respectievelijk voor.

Maak `main` een functie en voeg er de volgende code aan toe om een client te instantiëren met uw eindpunt en sleutel.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

## <a name="analyze-an-image"></a>Een afbeelding analyseren

De volgende code gebruikt het clientobject om een externe afbeelding te analyseren en de resultaten op de console af te drukken. U een tekstbeschrijving, categorisering, lijst met tags, gedetecteerde objecten, gedetecteerde merken, gedetecteerde gezichten, vlaggen met inhoud voor volwassenen, hoofdkleuren en afbeeldingstype krijgen.

### <a name="set-up-test-image"></a>Testafbeelding instellen

Sla eerst een verwijzing op naar de URL van de afbeelding die u wilt analyseren. Stop dit `main` in je functie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_url)]

> [!NOTE]
> U ook een lokale afbeelding analyseren. Zie de voorbeeldcode op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) voor scenario's met lokale afbeeldingen.

### <a name="specify-visual-features"></a>Visuele functies opgeven

Met de volgende functieaanroepen worden verschillende visuele functies uit de voorbeeldafbeelding geëxtraheerd. U definieert deze functies in de volgende secties.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze)]

### <a name="get-image-description"></a>Afbeeldingsbeschrijving downloaden

Met de volgende functie wordt de lijst met gegenereerde bijschriften voor de afbeelding weergegeven. Zie [Afbeeldingen beschrijven](../../concept-describing-images.md)voor meer informatie over de beschrijving van afbeeldingen.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_describe)]

### <a name="get-image-category"></a>Afbeeldingscategorie oppakken

Met de volgende functie wordt de gedetecteerde categorie van de afbeelding opdeine. Zie [Afbeeldingen categoriseren voor](../../concept-categorizing-images.md)meer informatie .

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_categorize)]

### <a name="get-image-tags"></a>Afbeeldingstags opmaken

Met de volgende functie wordt de set gedetecteerde tags in de afbeelding weergegeven. Zie [Inhoudstags voor](../../concept-tagging-images.md)meer informatie .

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_tags)]

### <a name="detect-objects"></a>Objecten detecteren

De volgende functie detecteert algemene objecten in de afbeelding en drukt deze af op de console. Zie [Objectdetectie](../../concept-object-detection.md)voor meer informatie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_objects)]

### <a name="detect-brands"></a>Merken detecteren

De volgende code detecteert bedrijfsmerken en logo's in de afbeelding en drukt deze af op de console. Voor meer informatie, [Merkdetectie](../../concept-brand-detection.md).

Declareer eerst een verwijzing naar `main` een nieuwe afbeelding in uw functie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brand_url)]

De volgende code definieert de merkdetectiefunctie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brands)]

### <a name="detect-faces"></a>Gezichten detecteren

Met de volgende functie worden de gedetecteerde gezichten in de afbeelding geretourneerd met hun rechthoekcoördinaten en bepaalde gezichtskenmerken. Zie [Gezichtsherkenning](../../concept-detecting-faces.md)voor meer informatie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Detecteer inhoud voor volwassenen, racy of bloederige

Met de volgende functie wordt de gedetecteerde aanwezigheid van inhoud voor volwassenen in de afbeelding afgedrukt. Voor meer informatie, zie [Adult, racy, bloederige inhoud](../../concept-detecting-adult-content.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Afbeeldingskleurenschema krijgen

Met de volgende functie worden de gedetecteerde kleurkenmerken in de afbeelding afgedrukt, zoals de dominante kleuren en accentkleur. Zie [Kleurenschema's voor](../../concept-detecting-color-schemes.md)meer informatie .

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Domeinspecifieke inhoud opmaken

Computer Vision kan gespecialiseerde modellen gebruiken om verdere analyse van beelden te doen. Zie [Domeinspecifieke inhoud](../../concept-detecting-domain-content.md)voor meer informatie. 

De volgende code ontleedt gegevens over gedetecteerde beroemdheden in de afbeelding.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_celebs)]

De volgende code onteert gegevens over gedetecteerde oriëntatiepunten in de afbeelding.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Het afbeeldingstype weergeven

Met de volgende functie wordt&mdash;informatie over het type afbeelding afgedrukt, of het nu gaat om illustraties of een lijntekening.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Gedrukte en handgeschreven tekst lezen

Computer Vision kan zichtbare tekst in een afbeelding lezen en converteren naar een tekenstroom. De code in deze sectie `RecognizeTextReadAPIRemoteImage`definieert een functie, die het clientobject gebruikt om afgedrukte of handgeschreven tekst in de afbeelding te detecteren en te extraheren.

Voeg de referentie- en functieaanroep voor voorbeeldafbeeldingen toe aan uw `main` functie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!NOTE]
> U ook tekst uit een lokale afbeelding extraheren. Zie de voorbeeldcode op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) voor scenario's met lokale afbeeldingen.

### <a name="call-the-read-api"></a>De API lezen aanroepen

Definieer de nieuwe functie `RecognizeTextReadAPIRemoteImage`voor het lezen van tekst. Voeg de onderstaande code toe, die de **batchreadbestandmethode** voor de gegeven afbeelding aanroept. Met deze methode wordt een bewerkings-id geretourneerd en wordt een asynchrone proces gestart om de inhoud van de afbeelding te lezen.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>Lees resultaten

Download vervolgens de bewerkings-id terug van de **BatchReadFile-aanroep** en gebruik deze met de methode **GetReadOperationResult** om de service op te vragen voor bewerkingsresultaten. De volgende code controleert de bewerking met intervallen van één seconde totdat de resultaten zijn geretourneerd. Vervolgens worden de geëxtraheerde tekstgegevens naar de console afgedrukt.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>Leesresultaten weergeven

Voeg de volgende code toe aan parse en geef de opgehaalde tekstgegevens weer en voltooi de functiedefinitie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit uw `go run` toepassingsmap uit met de opdracht.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een abonnement voor Cognitive Services wilt opschonen en verwijderen, u de bron- of brongroep verwijderen. Als u de brongroep verwijdert, worden ook andere bronnen verwijderd die eraan zijn gekoppeld.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verwijzing naar computervision-API (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)

* [Wat is Computer Vision?](../../Home.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go)
