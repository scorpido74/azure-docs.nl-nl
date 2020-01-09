---
title: 'Snelstartgids: Bing Aangepaste zoekopdrachten-client bibliotheek voor Java'
description: Ga aan de slag met de Bing Aangepaste zoekopdrachten-client bibliotheek voor Java door Zoek resultaten te vragen van uw Bing Aangepaste zoekopdrachten-exemplaar in deze Quick Start.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 1060cceb9241d9c1c490f7e7f12490c734b0a78d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75384550"
---
# <a name="quickstart-bing-custom-search-client-library-for-java"></a>Snelstartgids: Bing Aangepaste zoekopdrachten-client bibliotheek voor Java

Ga aan de slag met de Bing Aangepaste zoekopdrachten-client bibliotheek voor Java. Volg deze stappen om het pakket te installeren en de voorbeeld code voor basis taken uit te proberen. Met de Bing Custom Search-API kunt u op maat gemaakte, advertenties gratis zoek functies maken voor onderwerpen die u vindt.

Gebruik de Bing Aangepaste zoekopdrachten-client bibliotheek voor Java om het volgende te doen:

* Zoek resultaten zoeken op Internet vanuit uw Bing Aangepaste zoekopdrachten-exemplaar. 

[Naslag informatie over](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-java-stable) |  | [artefact (Maven) | -](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customsearch/) voor [beelden](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) van de [bibliotheek bron code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak een gratis versie](https://azure.microsoft.com/free/).
* De huidige versie van de [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* Het [hulp programma Gradle build](https://gradle.org/install/)of een andere afhankelijkheids Manager.
* Een Bing Custom Search-exemplaar. Zie [Quick Start: uw eerste Bing aangepaste zoekopdrachten-exemplaar maken](quick-start.md) voor meer informatie.

## <a name="setting-up"></a>Instellen

### <a name="create-a-bing-custom-search-azure-resource"></a>Een Bing Aangepaste zoekopdrachten Azure-resource maken

Azure-Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een resource voor Bing Aangepaste zoekopdrachten met behulp van de [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure cli](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine. U kunt ook het volgende doen:

* Ontvang een [proef versie](https://azure.microsoft.com/try/cognitive-services/#decision) die 7 dagen gratis geldig is. Nadat u zich hebt aangemeld, is deze beschikbaar op de [Azure-website](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Bekijk uw resource op het [Azure Portal](https://portal.azure.com/).

Nadat u een sleutel van uw proef abonnement of resource hebt ontvangen, [maakt u een omgevings variabele](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de sleutel met de naam `AZURE_BING_CUSTOM_SEARCH_API_KEY`.

### <a name="create-a-new-gradle-project"></a>Een nieuw Gradle-project maken

> [!TIP]
> Als u geen gebruik maakt van Gradle, kunt u de details van de client bibliotheek vinden voor andere afhankelijkheids managers in de [centrale opslag plaats van Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

Maak in een console venster (zoals cmd, Power shell of bash) een nieuwe map voor uw app en navigeer ernaar. 

```console
mkdir myapp && cd myapp
```

Voer de `gradle init` opdracht uit vanuit de werkmap. Met deze opdracht maakt u essentiële build-bestanden voor Gradle, met inbegrip van een *Build. Gradle. KTS* -bestand dat tijdens runtime wordt gebruikt om uw toepassing te configureren.

```console
gradle init --type basic
```

Wanneer u wordt gevraagd om een **DSL** te kiezen, selecteert u **Kotlin**.

## <a name="install-the-client-library"></a>De client bibliotheek installeren 

Zoek *Build. gradle. KTS* en open het met uw favoriete IDE-of tekst editor. Kopieer vervolgens in deze build-configuratie. Zorg ervoor dat u de client bibliotheek onder `dependencies`opgeeft:

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

Maak een map voor uw voor beeld-app. Voer de volgende opdracht uit in de werkmap:

```console
mkdir src/main/java
```

Ga naar de nieuwe map en maak een bestand met de naam *BingCustomSearchSample. java*. Open het en voeg de volgende `import`-instructies toe:


[!code-java[import statements](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=imports)]

Een klasse met de naam `BingCustomSearchSample` maken

```java
public class BingCustomSearchSample {
}
```

Maak in de-klasse een `main` methode en een variabele voor de sleutel van uw resource. Als u de omgevings variabele hebt gemaakt nadat u de toepassing hebt gestart, sluit u de editor, IDE of shell waarmee deze wordt uitgevoerd om toegang te krijgen tot de variabele. U gaat de methoden later definiëren.

[!code-java[main method](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=main)]

## <a name="object-model"></a>Object model

De Bing Aangepaste zoekopdrachten-client is een [BingCustomSearchAPI](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) -object dat is gemaakt op basis van de methode [Authenticate ()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager.authenticate?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomSearchManager_authenticate_String_) van het [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager?view=azure-java-stable) -object. U kunt een zoek opdracht verzenden met behulp van de methode [BingCustomInstances. Search ()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) van de client.

De API-reactie is een [SearchResponse](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.models.searchresponse?view=azure-java-stable) -object dat informatie bevat over de zoek opdracht en de zoek resultaten.

## <a name="code-examples"></a>Codevoorbeelden

Deze code fragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Bing Aangepaste zoekopdrachten-client bibliotheek voor Java:

* [De client verifiëren](#authenticate-the-client)
* [Zoek resultaten ophalen uit uw aangepaste zoek exemplaar](#get-search-results-from-your-custom-search-instance)

## <a name="authenticate-the-client"></a>De client verifiëren

De methode Main moet een [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) -object bevatten dat uw sleutel gebruikt en aanroepen van de `authenticate()`.

```java
BingCustomSearchAPI client = BingCustomSearchManager.authenticate(subscriptionKey);
```

## <a name="get-search-results-from-your-custom-search-instance"></a>Zoek resultaten ophalen uit uw aangepaste zoek exemplaar

Gebruik de functie [BingCustomInstances. Search ()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) van de client om een zoek opdracht naar uw aangepaste exemplaar te verzenden. Stel de `withCustomConfig` in op uw aangepaste configuratie-ID, of standaard ingesteld op `1`. Nadat u een reactie van de API hebt opgehaald, controleert u of er Zoek resultaten zijn gevonden. Als dit het geval is, kunt u het eerste Zoek resultaat ophalen door de `webPages().value().get()` functie van de reactie aan te roepen en de naam van het resultaat en de URL af te drukken. 

[!code-java[call the custom search API](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=runSample)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Bouw de app met de volgende opdracht uit de hoofd directory van uw project:

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
> [Een web-app voor aangepaste zoekopdrachten bouwen](./tutorials/custom-search-web-page.md)
