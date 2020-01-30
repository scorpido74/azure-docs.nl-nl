---
title: 'Snelstartgids: Computer Vision-client bibliotheek voor Go'
titleSuffix: Azure Cognitive Services
description: Ga aan de slag met de Computer Vision-client bibliotheek voor deze Snelstartgids.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: e4ac76f83ad4bedb420c52606598095c66747f2d
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76765300"
---
# <a name="quickstart-computer-vision-client-library-for-go"></a>Snelstartgids: Computer Vision-client bibliotheek voor Go

Ga aan de slag met de Computer Vision-client bibliotheek voor go. Volg deze stappen om het pakket te installeren en de voorbeeld code voor basis taken uit te proberen. Computer Vision biedt u toegang tot geavanceerde algoritmen voor het verwerken van afbeeldingen en het retour neren van gegevens.

Gebruik de Computer Vision-client bibliotheek voor Ga naar:

* Analyseer een afbeelding voor Tags, tekst beschrijving, gezichten, inhoud voor volwassenen en meer.
* Gedrukte en handgeschreven tekst herkennen met de batch-API voor lezen.

[Referentie documentatie](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision) | [bron code](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision) van de bibliotheek | [pakket](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De nieuwste versie van [Go](https://golang.org/dl/)

## <a name="setting-up"></a>Instellen

### <a name="create-a-computer-vision-azure-resource"></a>Een Computer Vision Azure-resource maken

Azure-Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een resource voor Computer Vision met behulp van de [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure cli](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine. U kunt ook het volgende doen:

* Ontvang een [proef sleutel](https://azure.microsoft.com/try/cognitive-services/#decision) die zeven dagen gratis geldig is. Nadat u zich hebt aangemeld, is deze beschikbaar op de [Azure-website](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Bekijk uw resource op het [Azure Portal](https://portal.azure.com/).

Nadat u een sleutel van uw proef abonnement of resource hebt ontvangen, moet u [omgevings variabelen maken](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de sleutel en eind punt-URL, respectievelijk met de naam `COMPUTER_VISION_SUBSCRIPTION_KEY` en `COMPUTER_VISION_ENDPOINT`.

### <a name="create-a-go-project-directory"></a>Een go-projectmap maken

Maak in een console venster (cmd, Power shell, Terminal, bash) een nieuwe werk ruimte voor uw Go-project met de naam `my-app`en navigeer ernaar.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Uw werk ruimte bevat drie mappen:

* **src** -deze map bevat bron code en pakketten. Alle pakketten die met de `go get` opdracht worden geïnstalleerd, worden in deze map geplaatst.
* **pakket** -deze map bevat de gecompileerde go package-objecten. Deze bestanden hebben allemaal een `.a`-extensie.
* **bin** : deze map bevat de binaire uitvoer bare bestanden die worden gemaakt wanneer u `go install`uitvoert.

> [!TIP]
> Zie de [Go-taal documentatie](https://golang.org/doc/code.html#Workspaces)voor meer informatie over de structuur van een go-werk ruimte. Deze hand leiding bevat informatie over het instellen van `$GOPATH` en `$GOROOT`.

### <a name="install-the-client-library-for-go"></a>De client bibliotheek voor Go installeren

Installeer vervolgens de client bibliotheek voor Go:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

of, als u dep gebruikt, binnen de uitvoer van de opslagplaats:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Een go-toepassing maken

Maak vervolgens een bestand in de map **src** met de naam `sample-app.go`:

```bash
cd src
touch sample-app.go
```

Open `sample-app.go` in de IDE-of tekst editor van uw voor keur. Voeg vervolgens de naam van het pakket toe en importeer de volgende bibliotheken:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports)]

Declareer ook een context in de hoofdmap van uw script. U hebt dit object nodig om de meeste Computer Vision functie aanroepen uit te voeren:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_context)]

Vervolgens begint u met het toevoegen van code om verschillende Computer Vision bewerkingen uit te voeren.

## <a name="object-model"></a>Object model

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Computer Vision go-SDK.

|Name|Beschrijving|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Deze klasse is nodig voor alle Computer Vision functionaliteit, zoals het analyseren van afbeeldingen en het lezen van tekst. U maakt de app met uw abonnements gegevens en gebruikt deze om de meeste installatie kopieën uit te voeren.|
|[ImageAnalysis](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ImageAnalysis)| Dit type bevat de resultaten van een **AnalyzeImage** -functie aanroep. Er zijn soort gelijke typen voor elk van de categorie-specifieke functies.|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| Dit type bevat de resultaten van een batch-Lees bewerking. |
|[VisualFeatureTypes](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#VisualFeatureTypes)| Dit type definieert de verschillende soorten afbeeldings analyse die kan worden uitgevoerd in een standaard analyse bewerking. U geeft een set VisualFeatureTypes-waarden op, afhankelijk van uw behoeften. |

## <a name="code-examples"></a>Code voorbeelden

Deze code fragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Computer Vision-client bibliotheek voor Go:

* [De client verifiëren](#authenticate-the-client)
* [Een afbeelding analyseren](#analyze-an-image)
* [Gedrukte en handgeschreven tekst lezen](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> Bij deze stap wordt ervan uitgegaan dat u [omgevings variabelen hebt gemaakt](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de computer vision sleutel en het eind punt met de naam `COMPUTER_VISION_SUBSCRIPTION_KEY` en `COMPUTER_VISION_ENDPOINT` respectievelijk.

Maak een `main`-functie en voeg de volgende code toe om een client met uw eind punt en sleutel te instantiëren.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

## <a name="analyze-an-image"></a>Analyseer een afbeelding

De volgende code gebruikt het client object voor het analyseren van een externe installatie kopie en het afdrukken van de resultaten naar de-console. U kunt een beschrijving, categorisatie, lijst met tags, gedetecteerde objecten, gedetecteerde merken, gedetecteerde gezichten, inhouds vlaggen voor volwassenen, hoofd kleuren en afbeeldings type ophalen.

### <a name="set-up-test-image"></a>Test installatie kopie instellen

Sla eerst een verwijzing op naar de URL van de afbeelding die u wilt analyseren. Plaats dit binnen de functie `main`.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_url)]

> [!NOTE]
> U kunt ook een lokale installatie kopie analyseren. Zie de voorbeeld code op [github](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) voor scenario's met betrekking tot lokale installatie kopieën.

### <a name="specify-visual-features"></a>Visuele functies opgeven

Met de volgende functie aanroepen worden verschillende visuele functies uit de voorbeeld afbeelding geëxtraheerd. U definieert deze functies in de volgende secties.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze)]

### <a name="get-image-description"></a>Beschrijving van afbeelding ophalen

De volgende functie haalt de lijst met gegenereerde bijschriften voor de afbeelding op. Zie [afbeeldingen beschrijven](../concept-describing-images.md)voor meer informatie over de beschrijving van de installatie kopie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_describe)]

### <a name="get-image-category"></a>Categorie van installatie kopie ophalen

De volgende functie haalt de gedetecteerde categorie van de installatie kopie op. Zie [afbeeldingen categoriseren](../concept-categorizing-images.md)voor meer informatie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_categorize)]

### <a name="get-image-tags"></a>Afbeeldings Tags ophalen

De volgende functie haalt de set gedetecteerde labels in de installatie kopie op. Zie [inhouds Tags](../concept-tagging-images.md)voor meer informatie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_tags)]

### <a name="detect-objects"></a>Objecten detecteren

Met de volgende functie worden algemene objecten in de installatie kopie gedetecteerd en afgedrukt in de-console. Zie [object detectie](../concept-object-detection.md)voor meer informatie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_objects)]

### <a name="detect-brands"></a>Brands detecteren

Met de volgende code worden de bedrijfs merken en-logo's in de installatie kopie gedetecteerd en afgedrukt in de-console. Voor meer informatie, [merk detectie](../concept-brand-detection.md).

Declareer eerst een verwijzing naar een nieuwe installatie kopie binnen uw `main`-functie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brand_url)]

Met de volgende code wordt de merk detectie functie gedefinieerd.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brands)]

### <a name="detect-faces"></a>Gezichten detecteren

Met de volgende functie worden de gedetecteerde gezichten in de afbeelding met hun rechthoek coördinaten en bepaalde gezichts kenmerken geretourneerd. Zie [gezichts detectie](../concept-detecting-faces.md)voor meer informatie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Inhoud voor volwassenen, ongepaste of benchmarks detecteren

Met de volgende functie wordt de gedetecteerde aanwezigheid van inhoud voor volwassenen in de installatie kopie afgedrukt. Zie voor meer informatie [volwassene, ongepaste, benchmarks-inhoud](../concept-detecting-adult-content.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Kleuren schema afbeelding ophalen

Met de volgende functie worden de gedetecteerde kleur kenmerken in de afbeelding afgedrukt, zoals de dominante kleuren en accent kleur. Zie [kleuren schema's](../concept-detecting-color-schemes.md)voor meer informatie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Domein-specifieke inhoud ophalen

Computer Vision kunt gespecialiseerde modellen gebruiken om verdere analyse van installatie kopieën uit te voeren. Zie [Domain-specifieke inhoud](../concept-detecting-domain-content.md)voor meer informatie. 

Met de volgende code worden gegevens over gedetecteerde beroemdheden in de installatie kopie geparseerd.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_celebs)]

Met de volgende code worden gegevens over gedetecteerde bezienswaardigheden in de installatie kopie geparseerd.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Het afbeeldings type ophalen

De volgende functie drukt informatie af over het type afbeelding&mdash;of het een illustratie of lijn tekening is.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Gedrukte en handgeschreven tekst lezen

Computer Vision kunt zicht bare tekst in een afbeelding lezen en deze converteren naar een teken stroom. De code in deze sectie definieert een functie, `RecognizeTextReadAPIRemoteImage`, die het client object gebruikt voor het detecteren en extra heren van gedrukte of handgeschreven tekst in de afbeelding.

Voeg de voorbeeld afbeelding en functie aanroep toe aan de functie `main`.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!NOTE]
> U kunt ook tekst extra heren uit een lokale installatie kopie. Zie de voorbeeld code op [github](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) voor scenario's met betrekking tot lokale installatie kopieën.

### <a name="call-the-read-api"></a>De Lees-API aanroepen

Definieer de nieuwe functie voor het lezen van tekst, `RecognizeTextReadAPIRemoteImage`. Voeg de onderstaande code toe, die de **BatchReadFile** -methode voor de gegeven afbeelding aanroept. Deze methode retourneert een bewerkings-ID en start een asynchroon proces om de inhoud van de afbeelding te lezen.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>Lees resultaten ophalen

Vervolgens haalt u de bewerkings-ID op die wordt geretourneerd door de **BatchReadFile** -aanroep en gebruikt u deze met de methode **GetReadOperationResult** om de service te doorzoeken op de resultaten van de bewerking. Met de volgende code wordt de bewerking met een interval van één seconde gecontroleerd totdat de resultaten worden geretourneerd. Vervolgens worden de geëxtraheerde tekst gegevens afgedrukt naar de-console.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>Lees resultaten weer geven

Voeg de volgende code toe om de opgehaalde tekst gegevens te parseren en weer te geven, en voltooi de functie definitie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit vanuit de toepassingsmap met de opdracht `go run`.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Computer Vision-API referentie (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)

* [Wat is Computer Vision?](../Home.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).
