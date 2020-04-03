---
title: Bing Custom Search Java-clientbibliotheek snel aan de slag
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: 32e02d3a7c1af6d15e7f381807d80f19b94da38f
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80587174"
---
Ga aan de slag met de Bing Custom Search-clientbibliotheek voor Java. Volg deze stappen om het pakket te installeren en probeer de voorbeeldcode voor basistaken uit. Met de Bing Custom Search API u op maat gemaakte, advertentievrije zoekervaringen maken voor onderwerpen die u belangrijk vindt. De broncode voor dit voorbeeld is te vinden [op GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingCustomSearch)

Gebruik de Bing Custom Search-clientbibliotheek voor Java om:

* Zoek zoekresultaten op internet in uw instantie Bing Custom Search. 

[Voorbeeld van broncode](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-java-stable) | Artefact (Reference documentation[Library](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [Artifact)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customsearch/) | [Samples](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een.](https://azure.microsoft.com/free/)
* De huidige versie van de [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* Het [gradle-buildgereedschap](https://gradle.org/install/)of een andere afhankelijkheidsmanager.
* Een Bing Custom Search-exemplaar. Zie [Snelstart: Maak uw eerste voorbeeld van Bing Custom Search](../../quick-start.md) voor meer informatie.

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

Nadat u een sleutel uit uw proefabonnement of resource hebt opgehaald, [maakt u een omgevingsvariabele](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de sleutel met de naam `AZURE_BING_CUSTOM_SEARCH_API_KEY`.

### <a name="create-a-new-gradle-project"></a>Een nieuw Gradle-project maken

> [!TIP]
> Als u Gradle niet gebruikt, u de gegevens van de clientbibliotheek voor andere afhankelijkheidsbeheerders vinden op de [Centrale Opslagplaats van Maven.](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/)

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor uw app en navigeer ernaar. 

```console
mkdir myapp && cd myapp
```

Voer `gradle init` de opdracht uit in uw werkmap. Met deze opdracht worden essentiële buildbestanden voor Gradle gemaakt, waaronder een *build.gradle.kts-bestand* dat wordt gebruikt bij runtime om uw toepassing te configureren.

```console
gradle init --type basic
```

Wanneer u wordt gevraagd om een **DSL** te kiezen, selecteert u **Kotlin**.

## <a name="install-the-client-library"></a>De clientbibliotheek installeren 

Zoek *build.gradle.kts* en open het met uw favoriete IDE of teksteditor. Kopieer vervolgens in deze buildconfiguratie. Zorg ervoor dat u `dependencies`de clientbibliotheek opneemt onder:

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingCustomSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-customsearch:1.0.2")
}
```

Maak een map voor uw voorbeeld-app. Voer in uw werkmap de volgende opdracht uit:

```console
mkdir src/main/java
```

Navigeer naar de nieuwe map en maak een bestand genaamd *BingCustomSampleSample.java*. Open het en `import` voeg de volgende instructies toe:


[!code-java[import statements](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=imports)]

Een klasse maken met de naam`BingCustomSearchSample`

```java
public class BingCustomSearchSample {
}
```

Maak in de `main` klasse een methode en een variabele voor de sleutel van uw resource. Als u de omgevingsvariabele hebt gemaakt nadat u de toepassing hebt gestart, sluit en heropent u de editor, IDE of shell die deze uitvoert om toegang te krijgen tot de variabele. U zult de methoden later definiëren.

[!code-java[main method](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=main)]

## <a name="object-model"></a>Objectmodel

De client Bing Custom Search is een [BingCustomSearchAPI-object](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) dat is gemaakt met de methode [Authenticeren()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager.authenticate) van het [object BingCustomSearchManager.](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager?view=azure-java-stable) U een zoekaanvraag verzenden met de methode [BingCustomInstances.search() van](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) de client.

Het API-antwoord is een [object SearchResponse](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.models.searchresponse?view=azure-java-stable) met informatie over de zoekopdracht en de zoekresultaten.

## <a name="code-examples"></a>Codevoorbeelden

In deze codefragmenten ziet u hoe u de volgende taken uitvoeren met de Bing Custom Search-clientbibliotheek voor Java:

* [De client verifiëren](#authenticate-the-client)
* [Zoekresultaten ophalen van uw aangepaste zoekexemplaar](#get-search-results-from-your-custom-search-instance)

## <a name="authenticate-the-client"></a>De client verifiëren

Uw belangrijkste methode moet een [BingCustomSearchManager-object](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) bevatten `authenticate()`dat uw sleutel inneemt en de .

```java
BingCustomSearchAPI client = BingCustomSearchManager.authenticate(subscriptionKey);
```

## <a name="get-search-results-from-your-custom-search-instance"></a>Zoekresultaten ophalen van uw aangepaste zoekexemplaar

Gebruik de functie [BingCustomInstances.search()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) van de client om een zoekopdracht naar uw aangepaste instantie te verzenden. Stel `withCustomConfig` de aangepaste configuratie-id in `1`of standaard op . Controleer na het ontvangen van een reactie van de API of er zoekresultaten zijn gevonden. Als dat het zo is, krijgt u `webPages().value().get()` het eerste zoekresultaat door de functie van het antwoord aan te roepen en de naam en URL van het resultaat af te drukken. 

[!code-java[call the custom search API](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=runSample)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Bouw de app met de volgende opdracht uit de hoofdmap van uw project:

```console
gradle build
```

Voer de toepassing `run` uit met het doel:

```console
gradle run
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een abonnement voor Cognitive Services wilt opschonen en verwijderen, u de bron- of brongroep verwijderen. Als u de brongroep verwijdert, worden ook andere bronnen verwijderd die eraan zijn gekoppeld.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app voor aangepaste zoekopdrachten bouwen](../../tutorials/custom-search-web-page.md)
