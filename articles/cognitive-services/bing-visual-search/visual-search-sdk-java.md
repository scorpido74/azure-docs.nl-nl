---
title: 'Snelstart: Bing Visual Search-clientbibliotheek voor Java | Microsoft Documenten'
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75379488"
---
# <a name="quickstart-bing-visual-search-client-library-for-java"></a>Snelstart: Bing Visual Search-clientbibliotheek voor Java

Ga aan de slag met de Bing Visual Search-clientbibliotheek voor Java. Volg deze stappen om het pakket te installeren en probeer de voorbeeldcode voor basistaken uit. 

Gebruik de Bing Visual Search-clientbibliotheek voor Java om:

* Upload een afbeelding om een visueel zoekverzoek te verzenden.
* Krijg de token en visuele zoektags voor afbeeldingsinzichten.

[Voorbeeld van broncode](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingvisualsearch?view=azure-java-stable) | Artefact (Reference documentation[Library](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingVisualSearch) | [Artifact)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-visualsearch/) | [Samples](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van de [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Het [gradle-build-hulpprogramma](https://gradle.org/install/)of een andere afhankelijkheidsmanager

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

Nadat u een sleutel uit uw proefabonnement of resource hebt opgehaald, [maakt u een omgevingsvariabele](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de sleutel met de naam `BING_SEARCH_V7_SUBSCRIPTION_KEY`.

### <a name="create-a-new-gradle-project"></a>Een nieuw Gradle-project maken

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor uw app en navigeer ernaar. 

```console
mkdir myapp && cd myapp
```

Voer `gradle init` de opdracht uit in uw werkmap. Met deze opdracht worden essentiële buildbestanden voor Gradle gemaakt, inclusief *build.gradle.kts* die tijdens runtime worden gebruikt om uw toepassing te maken en te configureren.

```console
gradle init --type basic
```

Wanneer u wordt gevraagd om een **DSL** te kiezen, selecteert u **Kotlin**.

Zoek *build.gradle.kts* en open het met uw favoriete IDE of teksteditor. Kopieer het vervolgens in deze buildconfiguratie:

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

Maak een map voor uw voorbeeld-app. Voer in uw werkmap de volgende opdracht uit:

```console
mkdir -p src/main/java
```

Maak een map voor de afbeelding die u wilt uploaden naar de API. Plaats de afbeelding in de map **resources.**

```console
mkdir -p src/main/resources
``` 

Navigeer naar de nieuwe map en maak een bestand genaamd *BingVisualSearchSample.java*. Open het in uw voorkeurseditor of `import` IDE en voeg de volgende instructies toe:

[!code-java[Import statements](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=imports)]

Maak vervolgens een nieuwe klasse.

```java
public class BingVisualSearchSample {
}
```

Maak in de `main` toepassingsmethode variabelen voor het Azure-eindpunt en de sleutel van uw bron. Als u de omgevingsvariabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell sluiten en opnieuw openen om toegang te krijgen tot de variabele. Maak vervolgens `byte[]` een afbeelding voor de afbeelding die u uploadt. Maak `try` een blok voor de methoden die u later definieert en `toByteArray()`laad de afbeelding en converteer deze naar bytes met behulp van .

[!code-java[Main method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=main)]


### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Deze quickstart maakt gebruik van de gradle afhankelijkheidsmanager. U vindt de clientbibliotheek en informatie voor andere afhankelijkheidsmanagers op de [Maven Central Repository.](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/)

In het *build.gradle.kts-bestand* van uw project moet u `implementation` de clientbibliotheek als verklaring opnemen. 

```kotlin
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken uitvoeren met de Bing Visual Search-clientbibliotheek en Java:

* [De client verifiëren](#authenticate-the-client)
* [Een visueel zoekverzoek verzenden](#send-a-visual-search-request)
* [Het token van het afbeeldingsinzicht en visuele zoektags afdrukken](#print-the-image-insight-token-and-visual-search-tags)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> Met deze quickstart wordt ervan uitgegaan dat u [een omgevingsvariabele](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) hebt gemaakt voor uw Bing Visual Search-toets, genaamd `BING_SEARCH_V7_SUBSCRIPTION_KEY`.


Gebruik in uw hoofdmethode uw abonnementssleutel om een [BingVisualSearchAPI-object](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingvisualsearchapi?view=azure-java-stable) te instantiëren.

```csharp
BingVisualSearchAPI client = BingVisualSearchManager.authenticate(subscriptionKey);
```

## <a name="send-a-visual-search-request"></a>Een visueel zoekverzoek verzenden

Stuur in een nieuwe methode de matrix afbeeldingsbyte (die in de `main()` methode is gemaakt) met de methode [bingImages().visualSearch()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingimages.visualsearch?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_visualsearch_BingImages_visualSearch__) van de client. 

[!code-java[visualSearch() method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=visualSearch)]

## <a name="print-the-image-insight-token-and-visual-search-tags"></a>Het token van het afbeeldingsinzicht en visuele zoektags afdrukken

Controleer of het object [ImageKnowledge](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.models.imageknowledge?view=azure-java-stable) null is. Zo niet, print dan het token voor afbeeldingsinzichten, het aantal tags, het aantal acties en het eerste actietype.

[!code-java[Print token and tags](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=printVisualSearchResults)]

## <a name="run-the-application"></a>De toepassing uitvoeren

U de app bouwen met:

```console
gradle build
```

Voer de toepassing `run` uit met het doel:

```console
gradle run
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een abonnement voor Cognitive Services wilt opschonen en verwijderen, u de bron- of brongroep verwijderen. Als u de brongroep verwijdert, worden ook andere bronnen verwijderd die eraan zijn gekoppeld.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app van één pagina maken](tutorial-bing-visual-search-single-page-app.md).
