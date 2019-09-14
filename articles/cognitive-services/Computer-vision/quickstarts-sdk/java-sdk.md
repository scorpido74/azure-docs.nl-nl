---
title: 'Quickstart: Client bibliotheek voor Java Computer Vision'
titleSuffix: Azure Cognitive Services
description: Ga aan de slag met de Computer Vision-client bibliotheek voor Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 07/25/2019
ms.author: pafarley
ms.openlocfilehash: 16a487dc007526f685edb52726f5797303a30c11
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966992"
---
# <a name="quickstart-computer-vision-client-library-for-java"></a>Quickstart: Client bibliotheek voor Java Computer Vision

Ga aan de slag met de Computer Vision-client bibliotheek voor Java. Volg deze stappen om het pakket te installeren en de voorbeeld code voor basis taken uit te proberen. Computer Vision biedt u toegang tot geavanceerde algoritmen voor het verwerken van afbeeldingen en het retour neren van gegevens.

Gebruik de Computer Vision-client bibliotheek voor Java om het volgende te doen:

* Analyseer een afbeelding voor Tags, tekst beschrijving, gezichten, inhoud voor volwassenen en meer.

[](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable) | Voor[beelden](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0) van referentie documentatie[artefact (Maven)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | 

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van de [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Het [hulp programma Gradle build](https://gradle.org/install/)of een andere afhankelijkheids Manager.

## <a name="setting-up"></a>Instellen

### <a name="create-a-computer-vision-azure-resource"></a>Een Computer Vision Azure-resource maken

Azure-Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een resource voor Computer Vision met behulp van de [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure cli](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine. U kunt ook het volgende doen:

* Ontvang een [proef sleutel](https://azure.microsoft.com/try/cognitive-services/#decision) die zeven dagen gratis geldig is. Nadat u zich hebt aangemeld, is deze beschikbaar op de [Azure-website](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Bekijk uw resource op het [Azure Portal](https://portal.azure.com/).

Vervolgens kunt u [omgevings variabelen maken](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de sleutel-en service- `COMPUTER_VISION_SUBSCRIPTION_KEY` eindpunt `COMPUTER_VISION_ENDPOINT`teken reeks, respectievelijk met de naam en.

### <a name="create-a-new-gradle-project"></a>Een nieuw Gradle-project maken

Maak in een console venster (zoals cmd, Power shell of bash) een nieuwe map voor uw app en navigeer ernaar. 

```console
mkdir myapp && cd myapp
```

Voer de `gradle init` opdracht uit vanuit de werkmap. Met deze opdracht worden essentiële build-bestanden gemaakt voor Gradle, waaronder *Build. Gradle. KTS*, dat tijdens runtime wordt gebruikt om uw toepassing te maken en te configureren.

```console
gradle init --type basic
```

Wanneer u wordt gevraagd om een **DSL** te kiezen, selecteert u **Kotlin**.

Zoek *Build. gradle. KTS* en open het met uw favoriete IDE-of tekst editor. Kopieer vervolgens de volgende Build-configuratie. Deze configuratie definieert het project als een Java-toepassing waarvan het ingangs punt de klasse **ComputerVisionQuickstarts**is. De Computer Vision-bibliotheek wordt geïmporteerd.

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

Voer vanuit uw werkmap de volgende opdracht uit om een projectmap te maken:

```console
mkdir -p src/main/java
```

Ga naar de nieuwe map en maak een bestand met de naam *ComputerVisionQuickstarts. java*. Open het bestand in uw voorkeurs editor of IDE en voeg de `import` volgende-instructies toe:

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_imports)]

Voeg vervolgens een klassen definitie toe voor **ComputerVisionQuickstarts**.

### <a name="install-the-client-library"></a>De client bibliotheek installeren

Deze Snelstartgids maakt gebruik van de Gradle dependency Manager. U vindt de client bibliotheek en informatie voor andere afhankelijkheids managers in de [centrale maven-opslag plaats](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

Neem in het bestand *Build. gradle. KTS* van het project de computer vision-client bibliotheek op als een afhankelijkheid.

```kotlin
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.2-beta")
}
```

## <a name="object-model"></a>Object model

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Computer Vision Java SDK.

|Name|Description|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) | Deze klasse is nodig voor alle Computer Vision functionaliteit. U maakt de app met uw abonnements gegevens en gebruikt deze om instanties van andere klassen te maken.|
|[ComputerVision](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable)| Deze klasse is afkomstig van het client object en verwerkt rechtstreeks alle afbeeldings bewerkingen, zoals het analyseren van afbeeldingen, tekst detectie en het genereren van miniaturen.|
|[VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable)| Deze opsomming definieert de verschillende typen afbeeldings analyse die kunnen worden uitgevoerd in een standaard analyse bewerking. U geeft een set VisualFeatureTypes-waarden op, afhankelijk van uw behoeften. |

## <a name="code-examples"></a>Code voorbeelden

Deze code fragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Computer Vision-client bibliotheek voor Java:

* [De client verifiëren](#authenticate-the-client)
* [Een afbeelding analyseren](#analyze-an-image)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> In deze Quick Start wordt ervan uitgegaan dat u [een omgevings variabele](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) hebt gemaakt `COMPUTER_VISION_SUBSCRIPTION_KEY`voor uw computer vision sleutel met de naam.

Met de volgende code wordt `main` een methode toegevoegd aan uw klasse en worden er variabelen gemaakt voor het Azure-eind punt en de sleutel van uw resource. U moet uw eigen eindpunt teken reeks invoeren, die u kunt vinden door de **overzichts** sectie van de Azure portal te controleren. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_mainvars)]

Voeg vervolgens de volgende code toe om een [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) -object te maken en door te geven aan een andere methode (n), die u later wilt definiëren.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_client)]

> [!NOTE]
> Als u de omgevings variabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell waarmee deze wordt uitgevoerd, sluiten en opnieuw openen om toegang te krijgen tot de variabele.

## <a name="analyze-an-image"></a>Een afbeelding analyseren

Met de volgende code wordt een methode `AnalyzeLocalImage`gedefinieerd, die gebruikmaakt van het-client object voor het analyseren van een lokale installatie kopie en het afdrukken van de resultaten. De methode retourneert een beschrijving van de tekst, categorisatie, lijst met tags, gedetecteerde gezichten, inhouds vlaggen voor volwassenen, hoofd kleuren en afbeeldings type.

### <a name="set-up-test-image"></a>Test installatie kopie instellen

Maak eerst een **resources/** map in de map **src/main/** van het project en voeg een installatie kopie toe die u wilt analyseren. Voeg vervolgens de volgende methode definitie toe aan uw **ComputerVisionQuickstarts** -klasse. Wijzig, indien nodig, de waarde van `pathToLocalImage` de zodat deze overeenkomt met uw afbeeldings bestand. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_refs)]

> [!NOTE]
> U kunt ook een externe installatie kopie analyseren met behulp van de URL. Zie de voorbeeld code op [github](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/ComputerVisionQuickstart.java) voor scenario's met betrekking tot externe installatie kopieën.

### <a name="specify-visual-features"></a>Visuele functies opgeven

Geef vervolgens op welke visuele functies u wilt uitpakken in de analyse. Zie de [VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable) -inventarisatie voor een volledige lijst.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_features)]

### <a name="analyze"></a>Analyseren
Met deze methode worden gedetailleerde resultaten voor elke omvang van de afbeeldings analyse afgedrukt op de console. U wordt aangeraden deze methode aanroep rond te zetten in een try/catch-blok. De methode **analyzeImageInStream** retourneert een **ImageAnalysis** -object dat alle geëxtraheerde informatie bevat.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_analyze)]

In de volgende secties ziet u hoe u deze gegevens in detail kunt parseren.

### <a name="get-image-description"></a>Beschrijving van afbeelding ophalen

Met de volgende code wordt de lijst met gegenereerde bijschriften voor de afbeelding opgehaald. Zie [afbeeldingen beschrijven](../concept-describing-images.md) voor meer informatie.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_captions)]

### <a name="get-image-category"></a>Categorie van installatie kopie ophalen

Met de volgende code wordt de gedetecteerde categorie van de afbeelding opgehaald. Zie [installatie kopieën categoriseren](../concept-categorizing-images.md) voor meer informatie.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_category)]

### <a name="get-image-tags"></a>Afbeeldings Tags ophalen

Met de volgende code wordt de set gedetecteerde labels in de afbeelding opgehaald. Zie [inhouds Tags](../concept-tagging-images.md) voor meer informatie.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_tags)]

### <a name="detect-faces"></a>Gezichten detecteren

Met de volgende code worden de gedetecteerde gezichten in de afbeelding met hun rechthoek coördinaten als resultaat gegeven en selecteert u face Attributes. Zie [gezichts detectie](../concept-detecting-faces.md) voor meer informatie.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_faces)]

### <a name="detect-adult-or-racy-content"></a>Inhoud voor volwassenen of ongepaste detecteren

Met de volgende code wordt de gedetecteerde aanwezigheid van inhoud voor volwassenen of ongepaste in de installatie kopie afgedrukt. Zie [inhoud voor volwassenen en ongepaste](../concept-detecting-adult-content.md) voor meer informatie.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_adult)]

### <a name="get-image-color-scheme"></a>Kleuren schema afbeelding ophalen

Met de volgende code worden de gedetecteerde kleur kenmerken in de afbeelding afgedrukt, zoals de dominante kleuren en accent kleur. Zie [kleuren schema's](../concept-detecting-color-schemes.md) voor meer informatie.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_colors)]

### <a name="get-domain-specific-content"></a>Domein-specifieke inhoud ophalen

Computer Vision kunt een speciaal model gebruiken om verdere analyse van installatie kopieën uit te voeren. Zie [Domain-specifieke inhoud](../concept-detecting-domain-content.md) voor meer informatie. 

Met de volgende code worden gegevens over gedetecteerde beroemdheden in de installatie kopie geparseerd.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_celebrities)]

Met de volgende code worden gegevens over gedetecteerde bezienswaardigheden in de installatie kopie geparseerd.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_landmarks)]

### <a name="get-the-image-type"></a>Het afbeeldings type ophalen

Met de volgende code wordt informatie over het type afbeelding&mdash;afgedrukt, ongeacht of het een illustratie of lijn tekening is.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_imagetype)]

## <a name="run-the-application"></a>De toepassing uitvoeren

U kunt de app bouwen met:

```console
gradle build
```

Voer de toepassing uit met `gradle run` de opdracht:

```console
gradle run
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u de Computer Vision Java-bibliotheek kunt gebruiken om basis taken uit te voeren. Bekijk vervolgens de referentie documentatie voor meer informatie over de-bibliotheek.

> [!div class="nextstepaction"]
>[Computer Vision Referentie (Java)](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable)

* [Wat is Computer Vision?](../Home.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/ComputerVisionQuickstart.java).