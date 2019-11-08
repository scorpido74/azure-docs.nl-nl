---
title: 'Snelstartgids: Content Moderator-client bibliotheek voor Java'
titleSuffix: Azure Cognitive Services
description: Meer informatie over hoe u aan de slag kunt gaan met de Azure Cognitive Services Content Moderator-client bibliotheek voor Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 10/25/2019
ms.author: pafarley
ms.openlocfilehash: edc51be93ba209a1c60970e6fa1b47fca75048c6
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73744452"
---
# <a name="quickstart-content-moderator-client-library-for-java"></a>Snelstartgids: Content Moderator-client bibliotheek voor Java

Ga aan de slag met de Content Moderator-client bibliotheek voor Java. Volg deze stappen om het pakket te installeren en de voorbeeld code voor basis taken uit te proberen. Content Moderator is een cognitieve service waarmee tekst-, afbeeldings-en video-inhoud wordt gecontroleerd op materiaal dat mogelijk aanstootgevend, riskant of anderszins ongewenst is. Wanneer dergelijk materiaal wordt gevonden, past de service de relevante labels (vlaggen) op de inhoud toe. Uw app kan gelabelde inhoud vervolgens afhandelen om te voldoen aan de regelgeving of om een beoogde omgeving voor gebruikers te beheren.

Gebruik de Content Moderator-client bibliotheek voor Java om het volgende te doen:

* Gematige installatie kopieën voor inhoud van volwassenen of ongepaste, tekst of menselijke gezichten.

[Naslag informatie over](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-java-stable) | [artefact (Maven) | -](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) voor [beelden](https://docs.microsoft.com/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van de [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Het [hulp programma Gradle build](https://gradle.org/install/)of een andere afhankelijkheids Manager.

## <a name="setting-up"></a>Instellen

### <a name="create-a-content-moderator-azure-resource"></a>Een Content Moderator Azure-resource maken

Azure-Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een resource voor Content Moderator met behulp van de [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure cli](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine. U kunt ook het volgende doen:

* Ontvang een [proef sleutel](https://azure.microsoft.com/try/cognitive-services/#decision) die zeven dagen gratis geldig is. Nadat u zich hebt aangemeld, is deze beschikbaar op de [Azure-website](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Bekijk uw resource op het [Azure Portal](https://portal.azure.com/).

Nadat u een sleutel van uw proef abonnement of resource hebt opgehaald, [maakt u een omgevings variabele](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de sleutel met de naam `AZURE_CONTENTMODERATOR_KEY`.

### <a name="create-a-new-gradle-project"></a>Een nieuw Gradle-project maken

Maak in een console venster (zoals cmd, Power shell of bash) een nieuwe map voor uw app en navigeer ernaar. 

```console
mkdir myapp && cd myapp
```
Voer `gradle init` uit. Met deze opdracht worden essentiële build-bestanden gemaakt voor Gradle, waaronder *Build. Gradle. KTS*, dat tijdens runtime wordt gebruikt om uw toepassing te maken en te configureren. Voer de volgende opdracht uit vanuit uw werkmap:

```console
gradle init --type basic
```

Wanneer u wordt gevraagd om een build-script DSL te kiezen, selecteert u **Kotlin**.

Zoek *Build. gradle. KTS* en open het met uw favoriete IDE-of tekst editor. Kopieer vervolgens de volgende Build-configuratie. Deze configuratie definieert het project als een Java-toepassing waarvan het ingangs punt de klasse **ContentModeratorQuickstart**is. Hiermee worden de Content Moderator SDK en de Gson SDK voor JSON-serialisatie geïmporteerd.

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

Voer vanuit uw werkmap de volgende opdracht uit om een projectmap te maken.

```console
mkdir -p src/main/java
```

Maak vervolgens een bestand met de naam *ContentModeratorQuickstart. java* in de nieuwe map. Open het bestand in uw voorkeurs editor of IDE en importeer de volgende bibliotheken bovenaan:

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

## <a name="object-model"></a>Object model

De volgende klassen behandelen enkele van de belangrijkste functies van de Content Moderator Java SDK.

|Naam|Beschrijving|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|Deze klasse is nodig voor alle Content Moderator functionaliteit. U maakt de app met uw abonnements gegevens en gebruikt deze om instanties van andere klassen te maken.|
|[ImageModeration](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|Deze klasse biedt de functionaliteit voor het analyseren van installatie kopieën voor inhoud voor volwassenen, persoonlijke gegevens of menselijke gezichten.|
|[TextModerations](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|Deze klasse biedt de functionaliteit voor het analyseren van tekst voor taal-, Grove-, fout-en persoonlijke gegevens.|
|[Evaluatie](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews?view=azure-java-stable)|Deze klasse biedt de functionaliteit van de controle-Api's, met inbegrip van de methoden voor het maken van taken, aangepaste werk stromen en mensen Beoordelingen.|


## <a name="code-examples"></a>Code voorbeelden

Deze code fragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Content Moderator-client bibliotheek voor Java:

* [De client verifiëren](#authenticate-the-client)
* [Gemiddelde afbeeldingen](#moderate-images)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> Bij deze stap wordt ervan uitgegaan dat u [een omgevings variabele hebt gemaakt](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor uw content moderator sleutel met de naam `AZURE_CONTENTMODERATOR_KEY`.

Maak in de `main` methode van de toepassing een [ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable) -object met behulp van de eindpunt waarde van uw abonnement en de omgevings variabele voor de abonnements sleutel. 

> [!NOTE]
> Als u de omgevings variabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell waarmee deze wordt uitgevoerd, sluiten en opnieuw openen om toegang te krijgen tot de variabele.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]

## <a name="moderate-images"></a>Gemiddelde afbeeldingen

### <a name="get-images"></a>Installatie kopieën ophalen

Maak in de **src/main/** map van het project een map **resources** en navigeer ernaar. Maak vervolgens een nieuw tekst bestand, *ImageFiles. txt*. In dit bestand voegt u de Url's van installatie kopieën toe om&mdash;één URL op elke regel te analyseren. U kunt de volgende voorbeeld afbeeldingen gebruiken:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Helper-klasse definiëren

Voeg vervolgens in uw *ContentModeratorQuickstart. java* -bestand de volgende klassen definitie toe binnen de klasse **ContentModeratorQuickstart** . Deze binnenste klasse wordt later gebruikt in het proces voor afbeeldings toezicht.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]

### <a name="iterate-through-images"></a>Afbeeldingen door lopen

Voeg vervolgens de volgende code toe aan de onderkant van de `main` methode. Of u kunt deze toevoegen aan een andere methode die wordt aangeroepen vanuit `main`. Met deze code wordt stapsgewijs elke regel van het bestand _ImageFiles. txt_ beschreven.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Controleren op inhoud voor volwassenen/ongepaste
Deze regel code controleert de installatie kopie op de opgegeven URL voor inhoud voor volwassenen of ongepaste. Zie de conceptuele hand leiding voor afbeeldings toezicht voor informatie over deze voor waarden.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>Controleren op tekst
Met deze regel code wordt de afbeelding gecontroleerd op zicht bare tekst.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Controleren op gezichten
Met deze regel code wordt de afbeelding voor menselijke gezichten gecontroleerd.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

Sla tot slot de geretourneerde informatie op in de lijst `EvaluationData`.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>Resultaten afdrukken

Voeg na de `while`-lus de volgende code toe, waarmee de resultaten worden afgedrukt naar de-console en naar een uitvoer bestand, *src/main/resources/ModerationOutput. json*.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

Sluit de `try`-instructie en voeg een `catch`-instructie toe om de methode te volt ooien.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>De toepassing uitvoeren

U kunt de app bouwen met:

```console
gradle build
```

Voer de toepassing uit met de opdracht `gradle run`:

```console
gradle run
```

Ga vervolgens naar het bestand *src/main/resources/ModerationOutput. json* en Bekijk de resultaten van de inhoud van uw systeem beheerder.

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u de Content Moderator Java-bibliotheek kunt gebruiken om beheer taken uit te voeren. Lees vervolgens een conceptuele hand leiding voor meer informatie over de toezicht op installatie kopieën of andere media.

> [!div class="nextstepaction"]
>[Concepten van afbeeldings toezicht](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Wat is Azure Content Moderator?](./overview.md)
* De broncode voor dit voorbeeld is te vinden [op GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/blob/master/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java).