---
title: 'Quickstart: Content Moderator Java-clientbibliotheek'
titleSuffix: Azure Cognitive Services
description: In deze quickstart leert u hoe u aan de slag kunt gaan met de Azure Cognitive Services Content Moderator-clientbibliotheek voor Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 01/27/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 4d4e09bed1350a59848ef6853efe2a301d7357e9
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87375719"
---
Ga aan de slag met de Content Moderator-clientbibliotheek voor Java. Volg deze stappen om het pakket te installeren en de voorbeeldcode voor basistaken uit te proberen. Content Moderator is een cognitieve service waarmee tekst, afbeeldingen en video-inhoud van materiaal wordt gecontroleerd op mogelijk aanstootgevende, riskante of anderszins ongewenst inhoud. Wanneer dergelijk materiaal wordt gevonden, past de service de relevante labels (vlaggen) op de inhoud toe. Uw app kan gelabelde inhoud vervolgens afhandelen om te voldoen aan de regelgeving of om een beoogde omgeving voor gebruikers te beheren.

Gebruik de Content Moderator-clientbibliotheek voor Java om:

* Afbeeldingen modereren op inhoud voor volwassenen of racistische inhoud, tekst of menselijke gezichten.

[Referentiedocumentatie](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-java-stable) | [Artefact (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) | [Voorbeelden](https://docs.microsoft.com/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* De huidige versie van de [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Het [hulpprogramma Gradle](https://gradle.org/install/) of een andere afhankelijkheidsbeheerder.

## <a name="setting-up"></a>Instellen

### <a name="create-a-content-moderator-azure-resource"></a>Een Azure-resource voor Content Moderator maken

Azure Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een resource voor Content Moderator met behulp van de [Azure-portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine. U kunt ook het volgende doen:

* Uw resource weergeven in [Azure Portal](https://portal.azure.com/).

Nadat u een sleutel van uw resource hebt opgehaald, [maakt u een omgevingsvariabele](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de sleutel met de naam `AZURE_CONTENTMODERATOR_KEY`.

### <a name="create-a-new-gradle-project"></a>Een nieuw Gradle-project maken

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor de app, en navigeer naar deze map. 

```console
mkdir myapp && cd myapp
```
Voer `gradle init` uit. Met deze opdracht maakt u essentiële buildbestanden voor Gradle, inclusief *build.gradle.kts*, dat tijdens runtime wordt gebruikt om de toepassing te maken en te configureren. Voer de volgende opdracht uit vanuit uw werkmap:

```console
gradle init --type basic
```

Wanneer u wordt gevraagd om een buildscript-DSL te kiezen, selecteert u **Kotlin**.

Zoek *build.gradle.kts* op en open het met uw favoriete IDE of teksteditor. Kopieer het vervolgens in de volgende buildconfiguratie. Deze configuratie definieert het project als een Java-toepassing waarvan het toegangspunt de klasse **ContentModeratorQuickstart** is. Hiermee worden de Content Moderator-clientbibliotheek en de Gson-SDK voor JSON-serialisatie geïmporteerd.

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

Voer de volgende opdracht uit vanuit uw werkmap om een projectbronmap te maken.

```console
mkdir -p src/main/java
```

Maak vervolgens een bestand met de naam *ContentModeratorQuickstart.java* in de nieuwe map. Open het bestand in uw voorkeurseditor of IDE en importeer de volgende bibliotheken bovenaan:

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

## <a name="object-model"></a>Objectmodel

De volgende klassen worden gebruikt voor enkele van de belangrijkste functies van de Content Moderator Java-clientbibliotheek.

|Naam|Beschrijving|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|Deze klasse is nodig voor alle Content Moderator-functionaliteit. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om instanties van andere klassen te maken.|
|[ImageModeration](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|Deze klasse biedt de functionaliteit voor het analyseren van afbeeldingen op inhoud voor volwassenen, persoonlijke gegevens of menselijke gezichten.|
|[TextModerations](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|Deze klasse biedt de functionaliteit voor het analyseren van tekst op taal, grove taal, fouten en persoonlijke gegevens.|
|[Reviews](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews?view=azure-java-stable)|Deze klasse biedt de functionaliteit van de Review-API's, waaronder de methoden voor het maken van taken, aangepaste werkstromen en menselijke beoordelingen.|


## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Content Moderator-clientbibliotheek voor Java:

* [De client verifiëren](#authenticate-the-client)
* [Afbeeldingen modereren](#moderate-images)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> In deze stap wordt ervan uitgegaan dat u [een omgevingsvariabele hebt gemaakt](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor uw Content Moderator-sleutel, met de naam `AZURE_CONTENTMODERATOR_KEY`.

Maak in de `main`-methode van de toepassing een [ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)-object met behulp van de eindpuntwaarde van uw abonnement en de omgevingsvariabele voor de abonnementssleutel. 

> [!NOTE]
> Als u de omgevingsvariabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell waarmee deze wordt uitgevoerd, sluiten en opnieuw openen om toegang te krijgen tot de variabele.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]

## <a name="moderate-images"></a>Afbeeldingen modereren

### <a name="get-images"></a>Afbeeldingen ophalen

Maak in de map **src/main/** van uw project een map **Resources** en navigeer ernaar. Maak vervolgens een nieuw tekstbestand *ImageFiles.txt*. In dit bestand voegt u de URL's van te analyseren afbeeldingen toe, met&mdash;één URL per regel. U kunt de volgende voorbeeldafbeeldingen gebruiken:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Helperklasse definiëren

Voeg vervolgens in uw *ContentModeratorQuickstart.java*-bestand de volgende klassedefinitie toe aan de klasse **ContentModeratorQuickstart**. Deze binnenste klasse wordt later gebruikt in het proces voor afbeeldingsmoderatie.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]

### <a name="iterate-through-images"></a>Afbeeldingen doorlopen

Voeg daarna de volgende code aan het einde van de `main`-methode toe. U kunt deze ook toevoegen aan een afzonderlijke methode die wordt aangeroepen vanuit `main`. Met deze code wordt stapsgewijs elke regel van het bestand _ImageFiles. txt_ doorlopen.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Controleren op inhoud voor volwassenen/racistische inhoud
Met deze coderegel wordt de afbeelding op de opgegeven URL op inhoud voor volwassenen of racistische inhoud gecontroleerd. Zie de conceptuele handleiding voor afbeeldingsmoderatie voor informatie over deze voorwaarden.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>Controleren op tekst
Met deze coderegel wordt de afbeelding gecontroleerd op zichtbare tekst.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Controleren op gezichten
Met deze coderegel wordt de afbeelding gecontroleerd op menselijke gezichten.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

Sla tot slot de geretourneerde informatie op in de lijst `EvaluationData`.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>Resultaten afdrukken

Voeg na de `while`-lus de volgende code toe, waarmee de resultaten worden afgedrukt op de console en op een uitvoerbestand, *src/main/resources/ModerationOutput.json*.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

Sluit de `try`-instructie en voeg een `catch`-instructie toe om de methode uit te voeren.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>De toepassing uitvoeren

U kunt de app maken met:

```console
gradle build
```

De toepassing uitvoeren met de opdracht `gradle run`:

```console
gradle run
```

Ga vervolgens naar de bestand *src/main/resources/ModerationOutput.json* en bekijk de resultaten van de inhoud van uw inhoudsmoderatie.

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u de Content Moderator Java-bibliotheek kunt gebruiken om moderatietaken uit te voeren. Nu kunt u doorgaan en in conceptgids meer lezen over het modereren van afbeeldingen en andere media.

> [!div class="nextstepaction"]
>[Concepten voor afbeeldingsmoderatie](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Wat is Azure Content Moderator?](../../overview.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java).