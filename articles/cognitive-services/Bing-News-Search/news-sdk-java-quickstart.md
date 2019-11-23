---
title: 'Quickstart: Perform a news search using the SDK for Java - Bing News Search'
titleSuffix: Azure Cognitive Services
description: Gebruik deze snelstartgids om nieuws te zoeken met de Bing News Search-SDK voor Java en om het antwoord te verwerken.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: def964dd932f8a41a877a3d9568a8ec4825874b8
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383919"
---
# <a name="quickstart-search-for-news-with-the-bing-news-search-sdk-for-java"></a>Quickstart: Search for news with the Bing News Search SDK for Java

Gebruik deze quickstart om aan de slag te gaan met de Bing News Search-SDK voor Java om nieuws te zoeken. Hoewel Bing Nieuws zoeken een REST API heeft die compatibel is met de meeste programmeertalen, biedt de SDK een eenvoudige manier om de service in uw toepassingen te integreren. De broncode voor dit voorbeeld is te vinden [op GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch).

## <a name="prerequisites"></a>Vereisten

Installeer de afhankelijkheden van Bing News Search SDK met Maven, Gradle of een ander systeem voor afhankelijkheidsbeheer. Het Maven-POM-bestand vereist de volgende declaratie:

```xml
    <dependencies>
    <dependency>
        <groupId>com.microsoft.azure.cognitiveservices</groupId>
        <artifactId>azure-cognitiveservices-newssearch</artifactId>
        <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
    </dependencies>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Een project maken en initialiseren

Maak een nieuw Java-project in uw favoriete IDE of editor en importeer de volgende bibliotheken.

```java
import com.microsoft.azure.cognitiveservices.newssearch.*;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.NewsInner;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.NewsSearchAPIImpl;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.TrendingTopicsInner;
import com.microsoft.rest.credentials.ServiceClientCredentials;
import okhttp3.Interceptor;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.Response;
import java.io.IOException;
```

## <a name="create-a-search-client-and-store-credentials"></a>Een zoekclient maken en referenties opslaan

1. Maak een methode met de naam `getClient()` die een nieuwe `NewsSearchAPIImpl`-zoekclient retourneert. Voeg uw eindpunt toe als de eerste parameter voor het nieuwe `NewsSearchAPIImpl`-object, en een nieuw `ServiceClientCredentials`-object om uw referenties op te slaan.

    ```java
    public static NewsSearchAPIImpl getClient(final String subscriptionKey) {
        return new NewsSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                });
    }
    ```

2. Overschrijf de `applyCredentialsFilter()`-functie om het `ServiceClientCredentials`-object te maken. Geef een `OkHttpClient.Builder` door aan de methode en gebruik de `addNetworkInterceptor()`-methode van de opbouwfunctie voor het maken van uw referenties voor de SDK-aanroep.

    ```java
    new ServiceClientCredentials() {
        @Override
        public void applyCredentialsFilter(OkHttpClient.Builder builder) {
            builder.addNetworkInterceptor(
                    new Interceptor() {
                        @Override
                        public Response intercept(Chain chain) throws IOException {
                            Request request = null;
                            Request original = chain.request();
                            // Request customization: add request headers.
                            Request.Builder requestBuilder = original.newBuilder()
                                    .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
                            request = requestBuilder.build();
                            return chain.proceed(request);
                        }
                    });
        }
    });
    ```

## <a name="send-and-receive-a-search-request"></a>Een zoekaanvraag verzenden en ontvangen

1. Maak een methode die `getClient()` aanroept en een zoekopdracht verzendt naar de Bing News Search-service. Filter de zoekopdracht met de parameters *market* en *count*, en geef informatie weer over het eerste nieuwsresultaat van uw zoekopdracht: naam, URL, publicatiedatum, beschrijving, providernaam en geschat totaal aantal overeenkomsten.

    ```java
    public static void newsSearch(String subscriptionKey)
    {
        NewsSearchAPIImpl client = getClient(subscriptionKey);
        String searchTerm = "Quantum Computing";
    
        NewsInner newsResults = client.searchs().list(searchTerm, null, null, null,
                null, null, 100, null, "en-us",
                null, null, null, null, null,
                null, null);
    
        if (newsResults.value().size() > 0)
        {
            NewsArticle firstNewsResult = newsResults.value().get(0);
    
            System.out.println(String.format("TotalEstimatedMatches value: %d", newsResults.totalEstimatedMatches()));
            System.out.println(String.format("News result count: %d", newsResults.value().size()));
            System.out.println(String.format("First news name: %s", firstNewsResult.name()));
            System.out.println(String.format("First news url: %s", firstNewsResult.url()));
            System.out.println(String.format("First news description: %s", firstNewsResult.description()));
            System.out.println(String.format("First news published time: %s", firstNewsResult.datePublished()));
            System.out.println(String.format("First news provider: %s", firstNewsResult.provider().get(0).name()));
        }
        else
        {
            System.out.println("Couldn't find news results!");
        }
    
    }
    
    ```

2. Voeg uw zoekmethode toe aan een `main()`-methode om de code uit te voeren.

    ```java 
    public static void main(String[] args) {
        String subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
        NewsSearchSDK.newsSearch(subscriptionKey);
    }
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app met één pagina maken](tutorial-bing-news-search-single-page-app.md)
