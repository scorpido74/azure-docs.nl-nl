---
title: 'Snelstartgids: Bing Visual Search-client bibliotheek voor Java | Microsoft Docs'
description: Upload een afbeelding met behulp van de Bing Visual Search SDK en krijg inzicht in de afbeelding.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: 5a6d39fa6e10ad0ad102f9d25ffd252ec9e0fa8a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75379488"
---
# <a name="quickstart-bing-visual-search-client-library-for-java"></a>Snelstartgids: Bing Visual Search-client bibliotheek voor Java

Ga aan de slag met de Bing Visual Search-client bibliotheek voor Java. Volg deze stappen om het pakket te installeren en de voorbeeld code voor basis taken uit te proberen. 

Gebruik de Bing Visual Search-client bibliotheek voor Java om het volgende te doen:

* Upload een installatie kopie om een visuele zoek opdracht te verzenden.
* Down load het beeld inzicht token en de tags voor visuele zoeken.

[Naslag informatie over](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingvisualsearch?view=azure-java-stable) |  | [artefact (Maven) | -](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-visualsearch/) voor [beelden](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) van de [bibliotheek bron code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingVisualSearch)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van de [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Het [hulp programma Gradle build](https://gradle.org/install/)of een andere afhankelijkheids Manager

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

Nadat u een sleutel van uw proef abonnement of resource hebt ontvangen, [maakt u een omgevings variabele](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de sleutel met de naam `BING_SEARCH_V7_SUBSCRIPTION_KEY`.

### <a name="create-a-new-gradle-project"></a>Een nieuw Gradle-project maken

Maak in een console venster (zoals cmd, Power shell of bash) een nieuwe map voor uw app en navigeer ernaar. 

```console
mkdir myapp && cd myapp
```

Voer de `gradle init` opdracht uit vanuit de werkmap. Met deze opdracht worden essentiële build-bestanden gemaakt voor Gradle, waaronder *Build. Gradle. KTS* , dat tijdens runtime wordt gebruikt om uw toepassing te maken en te configureren.

```console
gradle init --type basic
```

Wanneer u wordt gevraagd om een **DSL** te kiezen, selecteert u **Kotlin**.

Zoek *Build. gradle. KTS* en open het met uw favoriete IDE-of tekst editor. Kopieer het vervolgens in deze buildconfiguratie:

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingVisualSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Maak een map voor uw voor beeld-app. Voer de volgende opdracht uit in de werkmap:

```console
mkdir -p src/main/java
```

Maak een map voor de installatie kopie die u wilt uploaden naar de API. Plaats de installatie kopie in de map **resources** .

```console
mkdir -p src/main/resources
``` 

Ga naar de nieuwe map en maak een bestand met de naam *BingVisualSearchSample. java*. Open het bestand in uw voorkeurs editor of IDE en voeg de volgende `import`-instructies toe:

[!code-java[Import statements](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=imports)]

Maak vervolgens een nieuwe klasse.

```java
public class BingVisualSearchSample {
}
```

Maak in de `main` methode van de toepassing variabelen voor het Azure-eind punt en de sleutel van uw resource. Als u de omgevings variabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell waarmee deze wordt uitgevoerd, sluiten en opnieuw openen om toegang te krijgen tot de variabele. Maak vervolgens een `byte[]` voor de installatie kopie die u wilt uploaden. Maak een `try` blok voor de methoden die u later gaat definiëren en laad de afbeelding en converteer deze naar bytes met behulp van `toByteArray()`.

[!code-java[Main method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=main)]


### <a name="install-the-client-library"></a>De client bibliotheek installeren

Deze Snelstartgids maakt gebruik van de Gradle dependency Manager. U vindt de client bibliotheek en informatie voor andere afhankelijkheids managers in de [centrale maven-opslag plaats](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

Zorg er in het bestand *Build. gradle. KTS* van het project voor dat u de client bibliotheek als `implementation`-instructie opneemt. 

```kotlin
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

## <a name="code-examples"></a>Codevoorbeelden

Deze code fragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Bing Visual Search-client bibliotheek en Java:

* [De client verifiëren](#authenticate-the-client)
* [Een visuele zoek opdracht verzenden](#send-a-visual-search-request)
* [Het afbeeldings inzicht token en de tags voor visuele Zoek opdrachten afdrukken](#print-the-image-insight-token-and-visual-search-tags)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> In deze Quick Start wordt ervan uitgegaan dat u [een omgevings variabele hebt gemaakt](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor uw Bing Visual Search sleutel met de naam `BING_SEARCH_V7_SUBSCRIPTION_KEY`.


Zorg er in uw hoofd methode voor dat u uw abonnements sleutel gebruikt voor het instantiëren van een [BingVisualSearchAPI](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingvisualsearchapi?view=azure-java-stable) -object.

```csharp
BingVisualSearchAPI client = BingVisualSearchManager.authenticate(subscriptionKey);
```

## <a name="send-a-visual-search-request"></a>Een visuele zoek opdracht verzenden

In een nieuwe methode verzendt u de byte matrix van de afbeelding (die is gemaakt in de `main()` methode) met behulp van de methode [bingImages (). visualSearch (](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingimages.visualsearch?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_visualsearch_BingImages_visualSearch__) ) van de client. 

[!code-java[visualSearch() method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=visualSearch)]

## <a name="print-the-image-insight-token-and-visual-search-tags"></a>Het afbeeldings inzicht token en de tags voor visuele Zoek opdrachten afdrukken

Controleer of het [ImageKnowledge](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.models.imageknowledge?view=azure-java-stable) -object null is. Als dat niet het geval is, kunt u de afbeeldings Insights-token, het aantal Tags, het aantal acties en het eerste actie type afdrukken.

[!code-java[Print token and tags](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=printVisualSearchResults)]

## <a name="run-the-application"></a>De toepassing uitvoeren

U kunt de app bouwen met:

```console
gradle build
```

Voer de toepassing uit met het `run` doel:

```console
gradle run
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app van één pagina maken](tutorial-bing-visual-search-single-page-app.md).
