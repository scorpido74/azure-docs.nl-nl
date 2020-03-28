---
title: 'Snelstart: Computer Vision-clientbibliotheek voor Java'
description: Ga in deze quickstart aan de slag met de Computer Vision-clientbibliotheek voor Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/19/2019
ms.author: pafarley
ms.openlocfilehash: dbe986145a223f1958f1945abfa189de90952f4a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80272639"
---
<a name="HOLTop"></a>

[Voorbeeld van referentiedocumentatie](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable) | [Artefact (Maven)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [Samples](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van de [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Het [gradle-buildgereedschap](https://gradle.org/install/)of een andere afhankelijkheidsmanager.

## <a name="setting-up"></a>Instellen

### <a name="create-a-computer-vision-azure-resource"></a>Een Azure-bron voor Computer Vision maken

Azure Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een bron voor Computer Vision met de [Azure-portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine. U kunt ook het volgende doen:

* Ontvang een [proefsleutel](https://azure.microsoft.com/try/cognitive-services/#decision) die zeven dagen gratis geldig is. Nadat u zich hebt aangemeld, is deze beschikbaar op de [Azure-website.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Uw bron weergeven op de [Azure-portal.](https://portal.azure.com/)

Maak vervolgens [omgevingsvariabelen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de tekenreeks sleutel- `COMPUTER_VISION_SUBSCRIPTION_KEY` en `COMPUTER_VISION_ENDPOINT`serviceeindpunt, benoemd en , respectievelijk.

### <a name="create-a-new-gradle-project"></a>Een nieuw Gradle-project maken

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor uw app en navigeer ernaar. 

```console
mkdir myapp && cd myapp
```

Voer `gradle init` de opdracht uit in uw werkmap. Met deze opdracht worden essentiële buildbestanden voor Gradle gemaakt, inclusief *build.gradle.kts,* die tijdens runtime wordt gebruikt om uw toepassing te maken en te configureren.

```console
gradle init --type basic
```

Wanneer u wordt gevraagd om een **DSL** te kiezen, selecteert u **Kotlin**.

Zoek *build.gradle.kts* en open het met uw favoriete IDE of teksteditor. Kopieer vervolgens in de volgende buildconfiguratie. Deze configuratie definieert het project als een Java-toepassing waarvan het ingangspunt de klasse **ComputerVisionQuickstarts**is. Het importeert de Computer Vision bibliotheek.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "ComputerVisionQuickstarts"
}
repositories {
    mavenCentral()
}
```

Voer in uw werkmap de volgende opdracht uit om een projectbronmap te maken:

```console
mkdir -p src/main/java
```

Navigeer naar de nieuwe map en maak een bestand met de naam *ComputerVisionQuickstarts.java*. Open het in uw voorkeurseditor of `import` IDE en voeg de volgende instructies toe:

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imports)]

Voeg vervolgens een klassendefinitie toe voor **ComputerVisionQuickstarts**.

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Deze quickstart maakt gebruik van de gradle afhankelijkheidsmanager. U vindt de clientbibliotheek en informatie voor andere afhankelijkheidsmanagers op de [Maven Central Repository.](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/)

Neem in het *build.gradle.kts-bestand* van uw project de computervision-clientbibliotheek op als afhankelijkheid.

```kotlin
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.2-beta")
}
```

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces behandelen enkele van de belangrijkste functies van de Computer Vision Java SDK.

|Name|Beschrijving|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) | Deze klasse is nodig voor alle Computer Vision-functionaliteit. U wilt deze met uw abonnementsgegevens en u gebruikt deze om exemplaren van andere klassen te produceren.|
|[ComputerVisie](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable)| Deze klasse is afkomstig van het clientobject en verwerkt rechtstreeks alle afbeeldingsbewerkingen, zoals beeldanalyse, tekstdetectie en miniatuurgeneratie.|
|[VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable)| Dit enum definieert de verschillende soorten beeldanalyse die kunnen worden uitgevoerd in een standaard analysebewerking. U geeft een set VisualFeatureTypes-waarden op, afhankelijk van uw behoeften. |

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken uitvoeren met de Computer Vision-clientbibliotheek voor Java:

* [De client verifiëren](#authenticate-the-client)
* [Een afbeelding analyseren](#analyze-an-image)
* [Gedrukte en handgeschreven tekst lezen](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> Met deze quickstart wordt ervan uitgegaan dat u een `COMPUTER_VISION_SUBSCRIPTION_KEY` [omgevingsvariabele](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) hebt gemaakt voor uw Computer Vision-toets met de naam .

Met de volgende `main` code wordt een methode aan uw klasse toegevoegd en worden variabelen gemaakt voor het Azure-eindpunt en de sleutel van uw bron. U moet uw eigen eindpunttekenreeks invoeren, die u vinden door het gedeelte **Overzicht** van de Azure-portal te controleren. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_mainvars)]

Voeg vervolgens de volgende code toe om een [ComputerVisionClient-object](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) te maken en geeft deze door naar andere methoden, die u later zult definiëren.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_client)]

> [!NOTE]
> Als u de omgevingsvariabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell sluiten en opnieuw openen om toegang te krijgen tot de variabele.

## <a name="analyze-an-image"></a>Een afbeelding analyseren

De volgende code definieert een methode, `AnalyzeLocalImage`die het clientobject gebruikt om een lokale afbeelding te analyseren en de resultaten af te drukken. De methode retourneert een tekstbeschrijving, categorisering, lijst met tags, gedetecteerde gezichten, vlaggen met inhoud voor volwassenen, hoofdkleuren en afbeeldingstype.

### <a name="set-up-test-image"></a>Testafbeelding instellen

Maak eerst een **resources/map** in de **src/main/map** van uw project en voeg een afbeelding toe die u wilt analyseren. Voeg vervolgens de volgende methodedefinitie toe aan de klasse **ComputerVisionQuickstarts.** Wijzig indien nodig de `pathToLocalImage` waarde van het bestand dat overeenkomt met uw afbeeldingsbestand. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_refs)]

> [!NOTE]
> U ook een externe afbeelding analyseren met behulp van de URL. Zie de voorbeeldcode op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) voor scenario's met externe afbeeldingen.

### <a name="specify-visual-features"></a>Visuele functies opgeven

Geef vervolgens op welke visuele functies u wilt extraheren in uw analyse. Zie het [VisualFeatureTypes-enum](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable) voor een volledige lijst.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_features)]

### <a name="analyze"></a>Analyseren
Met deze methode worden gedetailleerde resultaten naar de console afgedrukt voor elk bereik van beeldanalyse. We raden je aan om deze methode aan te roepen in een Try/Catch-blok. De **methode analyzeImageInStream** retourneert een **imageanalysis-object** dat alle geëxtraheerde informatie bevat.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_analyze)]

In de volgende secties wordt uitgelegd hoe u deze informatie in detail ontzien.

### <a name="get-image-description"></a>Afbeeldingsbeschrijving downloaden

De volgende code krijgt de lijst met gegenereerde bijschriften voor de afbeelding. Zie [Afbeeldingen beschrijven voor](../../concept-describing-images.md)meer informatie .

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_captions)]

### <a name="get-image-category"></a>Afbeeldingscategorie oppakken

De volgende code krijgt de gedetecteerde categorie van de afbeelding. Zie [Afbeeldingen categoriseren voor](../../concept-categorizing-images.md)meer informatie .

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_category)]

### <a name="get-image-tags"></a>Afbeeldingstags opmaken

De volgende code krijgt de set gedetecteerde tags in de afbeelding. Zie [Inhoudstags voor](../../concept-tagging-images.md)meer informatie .

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_tags)]

### <a name="detect-faces"></a>Gezichten detecteren

De volgende code retourneert de gedetecteerde gezichten in de afbeelding met hun rechthoekcoördinaten en selecteert gezichtskenmerken. Zie [Gezichtsherkenning](../../concept-detecting-faces.md)voor meer informatie.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Detecteer inhoud voor volwassenen, racy of bloederige

Met de volgende code wordt de gedetecteerde aanwezigheid van inhoud voor volwassenen in de afbeelding afgedrukt. Voor meer informatie, zie [Adult, racy, bloederige inhoud](../../concept-detecting-adult-content.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_adult)]

### <a name="get-image-color-scheme"></a>Afbeeldingskleurenschema krijgen

In de volgende code worden de gedetecteerde kleurkenmerken in de afbeelding afgedrukt, zoals de dominante kleuren en accentkleur. Zie [Kleurenschema's voor](../../concept-detecting-color-schemes.md)meer informatie .

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_colors)]

### <a name="get-domain-specific-content"></a>Domeinspecifieke inhoud opmaken

Computer Vision kan gebruik maken van gespecialiseerde model om verdere analyse te doen op beelden. Zie [Domeinspecifieke inhoud](../../concept-detecting-domain-content.md)voor meer informatie. 

De volgende code ontleedt gegevens over gedetecteerde beroemdheden in de afbeelding.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_celebrities)]

De volgende code onteert gegevens over gedetecteerde oriëntatiepunten in de afbeelding.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_landmarks)]

### <a name="get-the-image-type"></a>Het afbeeldingstype weergeven

In de volgende code wordt&mdash;informatie over het type afbeelding afgedrukt, ongeacht of het om illustraties of lijntekening gaat.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imagetype)]

## <a name="read-printed-and-handwritten-text"></a>Gedrukte en handgeschreven tekst lezen

Computer Vision kan zichtbare tekst in een afbeelding lezen en converteren naar een tekenstroom.

> [!NOTE]
> U ook tekst in een externe afbeelding lezen met behulp van de URL. Zie de voorbeeldcode op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) voor scenario's met externe afbeeldingen.

### <a name="call-the-recognize-api"></a>De API herkennen aanroepen

Gebruik eerst de volgende code om de **methode recognizePrintedTextInStream** voor de gegeven afbeelding aan te roepen. Wanneer u deze code aan uw project toevoegt, `localTextImagePath` moet u de waarde van het pad naar uw lokale afbeelding vervangen. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_call)]

### <a name="print-recognize-results"></a>Resultaten van afdrukherkennen afdrukken

Het volgende codeblok verwerkt de geretourneerde tekst en onteinden deze om het eerste woord in elke regel uit te printen. U deze code gebruiken om snel inzicht te krijgen in de structuur van een **OcrResult-exemplaar.**

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_print)]

Sluit tot slot het try/catch-blok en de methodedefinitie af.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_catch)]

## <a name="run-the-application"></a>De toepassing uitvoeren

U de app bouwen met:

```console
gradle build
```

Voer de toepassing `gradle run` uit met de opdracht:

```console
gradle run
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een abonnement voor Cognitive Services wilt opschonen en verwijderen, u de bron- of brongroep verwijderen. Als u de brongroep verwijdert, worden ook andere bronnen verwijderd die eraan zijn gekoppeld.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart heb je geleerd hoe je de Computer Vision Java-bibliotheek gebruiken om basistaken uit te voeren. Bekijk vervolgens de referentiedocumentatie voor meer informatie over de bibliotheek.

> [!div class="nextstepaction"]
>[Computer Vision referentie (Java)](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable)

* [Wat is Computer Vision?](../../Home.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java)