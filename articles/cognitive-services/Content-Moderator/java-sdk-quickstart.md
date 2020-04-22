---
title: 'Snelstart: Content Moderator clientbibliotheek voor Java'
titleSuffix: Azure Cognitive Services
description: In deze quickstart leert u hoe u aan de slag met de azure cognitive services-inhoudsmoderatorclientbibliotheek voor Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: f86a54b53b73da9c798564d13d659844842e6f67
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768705"
---
# <a name="quickstart-content-moderator-client-library-for-java"></a>Snelstart: Content Moderator clientbibliotheek voor Java

Ga aan de slag met de contentmoderator-clientbibliotheek voor Java. Volg deze stappen om het pakket te installeren en probeer de voorbeeldcode voor basistaken uit. Content Moderator is een cognitieve service die tekst-, beeld- en video-inhoud controleert op materiaal dat mogelijk aanstootgevend, riskant of anderszins ongewenst is. Wanneer dergelijk materiaal wordt gevonden, past de service de relevante labels (vlaggen) op de inhoud toe. Uw app kan gelabelde inhoud vervolgens afhandelen om te voldoen aan de regelgeving of om een beoogde omgeving voor gebruikers te beheren.

Gebruik de contentmoderator-clientbibliotheek voor Java om:

* Matige afbeeldingen voor inhoud voor volwassenen of racy, tekst of menselijke gezichten.

[Voorbeeld van referentiedocumentatie](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-java-stable) | [Artefact (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) | [Samples](https://docs.microsoft.com/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van de [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Het [gradle-buildgereedschap](https://gradle.org/install/)of een andere afhankelijkheidsmanager.

## <a name="setting-up"></a>Instellen

### <a name="create-a-content-moderator-azure-resource"></a>Een Azure-bron voor inhoudsmoderator maken

Azure Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een bron voor Inhoudsmoderator met behulp van de [Azure-portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine. U kunt ook het volgende doen:

* Ontvang een [proefsleutel](https://azure.microsoft.com/try/cognitive-services/#decision) die zeven dagen gratis geldig is. Nadat u zich hebt aangemeld, is deze beschikbaar op de [Azure-website.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Uw bron weergeven op de [Azure-portal.](https://portal.azure.com/)

Nadat u een sleutel hebt opgehaald van uw proefabonnement of `AZURE_CONTENTMODERATOR_KEY`resource, maakt u een [omgevingsvariabele](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de sleutel met de naam .

### <a name="create-a-new-gradle-project"></a>Een nieuw Gradle-project maken

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor uw app en navigeer ernaar. 

```console
mkdir myapp && cd myapp
```
Voer `gradle init` uit. Met deze opdracht worden essentiële buildbestanden voor Gradle gemaakt, inclusief *build.gradle.kts,* die tijdens runtime wordt gebruikt om uw toepassing te maken en te configureren. Voer de volgende opdracht uit vanuit uw werkmap:

```console
gradle init --type basic
```

Wanneer u wordt gevraagd om een dsl-buildscript te kiezen, selecteert u **Kotlin**.

Zoek *build.gradle.kts* en open het met uw voorkeur IDE of teksteditor. Kopieer vervolgens in de volgende buildconfiguratie. Deze configuratie definieert het project als een Java-toepassing waarvan het ingangspunt de klasse **ContentModeratorQuickstart**is. Het importeert de Content Moderator SDK evenals de Gson sdk voor JSON serialisatie.

```kotlin
plugins {
    java
    application
}

application{ 
    mainClassName = "ContentModeratorQuickstart"
}

repositories{
    mavenCentral()
}

dependencies{
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-contentmoderator", version = "1.0.2-beta")
    compile(group = "com.google.code.gson", name = "gson", version = "2.8.5")
}
```

Voer in uw werkmap de volgende opdracht uit om een projectbronmap te maken.

```console
mkdir -p src/main/java
```

Maak vervolgens een bestand met de naam *ContentModeratorQuickstart.java* in de nieuwe map. Open het bestand in uw voorkeurseditor of IDE en importeer bovenaan de volgende bibliotheken:

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

## <a name="object-model"></a>Objectmodel

De volgende klassen behandelen enkele van de belangrijkste functies van de Content Moderator Java SDK.

|Naam|Beschrijving|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|Deze klasse is nodig voor alle functionaliteit van Content Moderator. U wilt deze met uw abonnementsgegevens en u gebruikt deze om exemplaren van andere klassen te produceren.|
|[ImageModeration](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|Deze klasse biedt de functionaliteit voor het analyseren van afbeeldingen voor inhoud voor volwassenen, persoonlijke informatie of menselijke gezichten.|
|[TextModerations](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|Deze klasse biedt de functionaliteit voor het analyseren van tekst voor taal, godslastering, fouten en persoonlijke informatie.|
|[Beoordelingen](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews?view=azure-java-stable)|Deze klasse biedt de functionaliteit van de Beoordelingens-API's, inclusief de methoden voor het maken van taken, aangepaste werkstromen en menselijke beoordelingen.|


## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken uitvoeren met de clientbibliotheek voor inhoudsmoderator voor Java:

* [De client verifiëren](#authenticate-the-client)
* [Matige afbeeldingen](#moderate-images)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> In deze stap wordt ervan uitgegaan dat u een `AZURE_CONTENTMODERATOR_KEY` [omgevingsvariabele](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) hebt gemaakt voor de sleutel Inhoudsmoderator, met de naam .

Maak in de `main` methode van de toepassing een [ContentModeratorClient-object](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable) met de variabele van uw abonnementseindpunten en de omgevingsvariabele van abonnementssleutel. 

> [!NOTE]
> Als u de omgevingsvariabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell sluiten en opnieuw openen om toegang te krijgen tot de variabele.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]

## <a name="moderate-images"></a>Matige afbeeldingen

### <a name="get-images"></a>Afbeeldingen ophalen

Maak in de **src/main/map** van uw project een **bronnenmap** en navigeer ernaar. Maak vervolgens een nieuw tekstbestand, *ImageFiles.txt*. In dit bestand voegt u de URL's van afbeeldingen toe om op elke regel één URL te analyseren.&mdash; U de volgende voorbeeldafbeeldingen gebruiken:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Helperklasse definiëren

Voeg vervolgens in je *ContentModeratorQuickstart.java-bestand* de volgende klassendefinitie toe in de klasse **ContentModeratorQuickstart.** Deze binnenklasse zal later worden gebruikt in het beeldmatigingsproces.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]

### <a name="iterate-through-images"></a>Herhalen door beelden

Voeg vervolgens de volgende code toe `main` aan de onderkant van de methode. U het ook toevoegen aan een aparte `main`methode die wordt aangeroepen vanaf. Deze code doorloopt elke regel van het bestand _ImageFiles.txt._

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Controleer op inhoud voor volwassenen/racy
Deze coderegel controleert de afbeelding op de opgegeven URL op inhoud voor volwassenen of racy. Zie de conceptueel handleiding voor beeldmatiging voor informatie over deze termen.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>Controleren op tekst
Deze coderegel controleert de afbeelding op zichtbare tekst.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Controleren op gezichten
Deze coderegel controleert de afbeelding op menselijke gezichten.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

Sla ten slotte de `EvaluationData` geretourneerde informatie op in de lijst.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>Afdrukresultaten

Voeg `while` na de lus de volgende code toe, die de resultaten afdrukt op de console en naar een uitvoerbestand, *src/main/resources/ModerationOutput.json*.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

Sluit de `try` instructie af `catch` en voeg een instructie toe om de methode te voltooien.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>De toepassing uitvoeren

U de app bouwen met:

```console
gradle build
```

Voer de toepassing `gradle run` uit met de opdracht:

```console
gradle run
```

Navigeer vervolgens naar het *bestand src/main/resources/ModerationOutput.json* en bekijk de resultaten van uw inhoudsmoderatie.

## <a name="clean-up-resources"></a>Resources opschonen

Als u een abonnement voor Cognitive Services wilt opschonen en verwijderen, u de bron- of brongroep verwijderen. Als u de brongroep verwijdert, worden ook andere bronnen verwijderd die eraan zijn gekoppeld.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart heb je geleerd hoe je de Java-bibliotheek voor inhoudsmoderator gebruiken om moderatietaken uit te voeren. Leer vervolgens meer over de moderatie van afbeeldingen of andere media door een conceptuele handleiding te lezen.

> [!div class="nextstepaction"]
>[Beeldmoderatieconcepten](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Wat is Azure Content Moderator?](./overview.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java)