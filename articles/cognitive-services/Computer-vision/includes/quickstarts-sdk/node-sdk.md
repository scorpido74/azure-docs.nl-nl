---
title: 'Snelstart: Computer Vision-clientbibliotheek voor Node.js'
description: Aan de slag met de Computer Vision-clientbibliotheek voor Node.js met deze quickstart
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 01/22/2020
ms.author: pafarley
ms.openlocfilehash: 234e2402287fd76bdfd96cb6884c3fd20e805d06
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80136019"
---
<a name="HOLTop"></a>

[Voorbeeld van het](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest) | [broncodepakket](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision) | van de naslagdocumentatiebibliotheek[(npm)](https://www.npmjs.com/package/@azure/cognitiveservices-computervision) | [Samples](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [Node.js](https://nodejs.org/)

## <a name="setting-up"></a>Instellen

### <a name="create-a-computer-vision-azure-resource"></a>Een Azure-bron voor Computer Vision maken

Azure Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een bron voor Computer Vision met de [Azure-portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine. U kunt ook het volgende doen:

* Ontvang een [proefsleutel](https://azure.microsoft.com/try/cognitive-services/#decision) die zeven dagen gratis geldig is. Nadat u zich hebt aangemeld, is deze beschikbaar op de [Azure-website.](https://azure.microsoft.com/try/cognitive-services/my-apis/)
* Uw bron weergeven op de [Azure-portal.](https://portal.azure.com/)

Nadat u een sleutel uit uw proefabonnement of resource hebt opgehaald, maakt `COMPUTER_VISION_SUBSCRIPTION_KEY` u `COMPUTER_VISION_ENDPOINT` [omgevingsvariabelen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de naam van de sleutel- en eindpunt-URL, met de naam en respectievelijk voor het eindpunt.

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor uw app en navigeer ernaar.

```console
mkdir myapp && cd myapp
```

Voer `npm init` de opdracht uit om een `package.json` knooppunttoepassing met een bestand te maken.

```console
npm init
```

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Installeer `ms-rest-azure` de `@azure/cognitiveservices-computervision` NPM-pakketten en NPM-pakketten:

```console
npm install @azure/cognitiveservices-computervision
```

Het bestand `package.json` van uw app wordt bijgewerkt met de afhankelijkheden.

### <a name="prepare-the-nodejs-script"></a>Het script Van Node.js voorbereiden

Maak een nieuw bestand, *index.js*en open het in een teksteditor. Voeg de volgende importinstructies toe.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports)]

Definieer vervolgens een `computerVision` functie en declareer een async-reeks met primaire functie en terugbelfunctie. U voegt uw quickstartcode toe aan `computerVision` de primaire functie en belt onderaan het script.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces behandelen enkele van de belangrijkste functies van de Computer Vision Node.js SDK.

|Name|Beschrijving|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) | Deze klasse is nodig voor alle Computer Vision-functionaliteit. U wilt deze instantiëren met uw abonnementsgegevens en u gebruikt deze om de meeste beeldbewerkingen uit te voeren.|
|[VisualFeatureTypes](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/visualfeaturetypes?view=azure-node-latest)| Dit enum definieert de verschillende soorten beeldanalyse die kunnen worden uitgevoerd in een standaard analysebewerking. U geeft een set **VisualFeatureTypes-waarden** op, afhankelijk van uw behoeften. |

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken uitvoeren met de computervision-clientbibliotheek voor Node.js:

* [De client verifiëren](#authenticate-the-client)
* [Een afbeelding analyseren](#analyze-an-image)
* [Gedrukte en handgeschreven tekst lezen](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>De client verifiëren

Maak variabelen voor het Azure-eindpunt en de sleutel van uw resource. Als u de omgevingsvariabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell sluiten en opnieuw openen om toegang te krijgen tot de variabele.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_vars)]

Instantiate een klant met uw eindpunt en sleutel. Maak een [Object ApiKeyCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.apikeycredentials?view=azure-python) met uw sleutel en eindpunt en gebruik het om een [ComputerVisionClient-object](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) te maken.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

## <a name="analyze-an-image"></a>Een afbeelding analyseren

De code in deze sectie analyseert externe afbeeldingen om verschillende visuele functies te extraheren. U deze bewerkingen uitvoeren als onderdeel van de **analyseImage-methode** van het clientobject, of u ze aanroepen met behulp van afzonderlijke methoden. Zie de [referentiedocumentatie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest) voor meer informatie.

> [!NOTE]
> U ook een lokale afbeelding analyseren. Zie de voorbeeldcode op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) voor scenario's met lokale afbeeldingen.

### <a name="get-image-description"></a>Afbeeldingsbeschrijving downloaden

De volgende code krijgt de lijst met gegenereerde bijschriften voor de afbeelding. Zie [Afbeeldingen beschrijven](../../concept-describing-images.md) voor meer details.

Definieer eerst de URL van een afbeelding die u wilt analyseren:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe_image)]

Voeg vervolgens de volgende code toe om de afbeeldingsbeschrijving te krijgen en af te drukken op de console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe)]

### <a name="get-image-category"></a>Afbeeldingscategorie oppakken

De volgende code krijgt de gedetecteerde categorie van de afbeelding. Zie [Afbeeldingen categoriseren](../../concept-categorizing-images.md) voor meer informatie.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories)]

Definieer de helperfunctie: `formatCategories`

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories_format)]

### <a name="get-image-tags"></a>Afbeeldingstags opmaken

De volgende code krijgt de set gedetecteerde tags in de afbeelding. Zie [Inhoudstags](../../concept-tagging-images.md) voor meer informatie.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tags)]

Definieer de helperfunctie: `formatTags`

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tagsformat)]

### <a name="detect-objects"></a>Objecten detecteren

De volgende code detecteert algemene objecten in de afbeelding en drukt deze af op de console. Zie [Objectdetectie](../../concept-object-detection.md) voor meer informatie.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objects)]

Definieer de helperfunctie: `formatRectObjects`

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objectformat)]

### <a name="detect-brands"></a>Merken detecteren

De volgende code detecteert bedrijfsmerken en logo's in de afbeelding en drukt deze af op de console. Zie [Merkdetectie](../../concept-brand-detection.md) voor meer details.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_brands)]

### <a name="detect-faces"></a>Gezichten detecteren

Met de volgende code worden de gedetecteerde gezichten in de afbeelding geretourneerd met hun rechthoekcoördinaten en selecteert u gezichtskenmerken. Zie [Gezichtsherkenning](../../concept-detecting-faces.md) voor meer details.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_faces)]

Definieer de helperfunctie: `formatRectFaces`

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_formatfaces)]

### <a name="detect-adult-racy-or-gory-content"></a>Detecteer inhoud voor volwassenen, racy of bloederige

Met de volgende code wordt de gedetecteerde aanwezigheid van inhoud voor volwassenen in de afbeelding afgedrukt. Zie [Adult, racy, bloederige inhoud](../../concept-detecting-adult-content.md) voor meer details.

Definieer de URL van de te gebruiken afbeelding:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult_image)]

Voeg vervolgens de volgende code toe om inhoud voor volwassenen te detecteren en de resultaten af te drukken op de console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Afbeeldingskleurenschema krijgen

In de volgende code worden de gedetecteerde kleurkenmerken in de afbeelding afgedrukt, zoals de dominante kleuren en accentkleur. Zie [Kleurenschema's](../../concept-detecting-color-schemes.md) voor meer informatie.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors)]

Definieer de helperfunctie `printColorScheme` om de details van het kleurenschema op de console af te drukken.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors_print)]

### <a name="get-domain-specific-content"></a>Domeinspecifieke inhoud opmaken

Computer Vision kan gebruik maken van gespecialiseerde model om verdere analyse te doen op beelden. Zie [Domeinspecifieke inhoud](../../concept-detecting-domain-content.md) voor meer informatie.

Definieer eerst de URL van een afbeelding die u wilt analyseren:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_domain_image)]

De volgende code onteert gegevens over gedetecteerde oriëntatiepunten in de afbeelding.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks)]

Definieer de helperfunctie `formatRectDomain` om de locatiegegevens over gedetecteerde oriëntatiepunten te ontzien.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks_rect)]

### <a name="get-the-image-type"></a>Het afbeeldingstype weergeven

In de volgende code wordt&mdash;informatie over het type afbeelding afgedrukt, ongeacht of het om illustraties of lijntekening gaat.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype)]

Definieer de helperfunctie: `describeType`

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype_describe)]

## <a name="read-printed-and-handwritten-text"></a>Gedrukte en handgeschreven tekst lezen

Computer Vision kan zichtbare tekst in een afbeelding lezen en converteren naar een tekenstroom.

> [!NOTE]
> U ook tekst uit een lokale afbeelding lezen. Zie de voorbeeldcode op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) voor scenario's met lokale afbeeldingen.

### <a name="set-up-test-images"></a>Testafbeeldingen instellen

Sla een verwijzing op naar de URL van de afbeeldingen waaruit u tekst wilt extraheren.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

### <a name="call-the-recognize-api"></a>De API herkennen aanroepen

Voeg de onderstaande code `recognizeText` toe, die de functie voor de gegeven afbeeldingen aanroept.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

Definieer `recognizeText` de functie. Hiermee wordt de **methode recognizeText** op het clientobject aangesproken, waarbij een bewerkings-id wordt geretourneerd en een asynchrone procedure wordt gestart om de inhoud van de afbeelding te lezen. Vervolgens wordt de bewerkings-id gebruikt om de bewerking met intervallen van één seconde te controleren totdat de resultaten zijn geretourneerd. Het retourneert vervolgens de geëxtraheerde resultaten.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

Definieer vervolgens de helperfunctie `printRecText`, die de resultaten van een bewerking Herkennen op de console afdrukt.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing `node` uit met de opdracht voor uw quickstartbestand.

```console
node index.js
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een abonnement voor Cognitive Services wilt opschonen en verwijderen, u de bron- of brongroep verwijderen. Als u de brongroep verwijdert, worden ook andere bronnen verwijderd die eraan zijn gekoppeld.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Verwijzing naar computervision-API (Node.js)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest)

* [Wat is Computer Vision?](../../Home.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js)
