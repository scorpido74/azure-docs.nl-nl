---
title: 'Quickstart: Search for videos using the SDK for Java - Bing Video Search'
titleSuffix: Azure Cognitive Services
description: Gebruik deze quickstart om zoekaanvragen voor video's te verzenden naar de Bing Video Search-SDK voor Java.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 06/26/2019
ms.author: aahi
ms.openlocfilehash: cbe5f0023b16aec1ed68e7a78ddb3103f40eb85b
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74378600"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-java"></a>Quickstart: Perform a video search with the Bing Video Search SDK for Java

Gebruik deze quickstart om aan de slag te gaan met de Bing Video Search-SDK voor Java om nieuws te zoeken. Hoewel Bing Video's zoeken een REST-API heeft die compatibel is met de meeste programmeertalen, biedt de SDK een eenvoudige manier om de service in uw toepassingen te integreren. The source code for this sample can be found on [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingVideoSearch), with additional annotations, and features.

## <a name="prerequisites"></a>Vereisten

* De [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)

* De [Gson-bibliotheek](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

Installeer de afhankelijkheden van Bing Video Search SDK met Maven, Gradle of een ander systeem voor afhankelijkheidsbeheer. Het Maven-POM-bestand vereist de volgende declaratie:

```xml
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-videosearch</artifactId>
      <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
  </dependencies> 
```

## <a name="create-and-initialize-a-project"></a>Een project maken en initialiseren


Maak een nieuw Java-project in uw favoriete IDE of editor en importeer de volgende bibliotheken.

    ```java
    import com.microsoft.azure.cognitiveservices.videosearch.*;
    import com.microsoft.azure.cognitiveservices.videosearch.VideoObject;
    import com.microsoft.rest.credentials.ServiceClientCredentials;
    import okhttp3.Interceptor;
    import okhttp3.OkHttpClient;
    import okhttp3.Request;
    import okhttp3.Response;
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.List; 
    ```

## <a name="create-a-search-client"></a>Een zoekclient maken

1. Implementeer de client `VideoSearchAPIImpl`, die uw API-eindpunt vereist, en een exemplaar van de klasse `ServiceClientCredentials`.

    ```java
    public static VideoSearchAPIImpl getClient(final String subscriptionKey) {
        return new VideoSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    Voer voor het implementeren van `ServiceClientCredentials` de volgende stappen uit:

    1. overschrijf de functie `applyCredentialsFilter()`, met een `OkHttpClient.Builder`-object als parameter. 
        
        ```java
        //...
        new ServiceClientCredentials() {
                @Override
                public void applyCredentialsFilter(OkHttpClient.Builder builder) {
                //...
                }
        //...
        ```
    
    2. Roep binnen `applyCredentialsFilter()` `builder.addNetworkInterceptor()` aan. Maak een nieuw `Interceptor`-object en overschrijf de methode `intercept()` ervan om een `Chain`-interceptorobject te nemen.

        ```java
        //...
        builder.addNetworkInterceptor(
            new Interceptor() {
                @Override
                public Response intercept(Chain chain) throws IOException {
                //...    
                }
            });
        ///...
        ```

    3. Maak binnen de functie `intercept` variabelen voor uw aanvraag. Gebruik `Request.Builder()` om uw aanvraag te maken. Voeg uw abonnementssleutel toe aan de `Ocp-Apim-Subscription-Key`-header en retourneer `chain.proceed()` in het aanvraagobject.
            
        ```java
        //...
        public Response intercept(Chain chain) throws IOException {
            Request request = null;
            Request original = chain.request();
            Request.Builder requestBuilder = original.newBuilder()
                    .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            request = requestBuilder.build();
            return chain.proceed(request);
        }
        //...
        ```

## <a name="send-a-search-request-and-receive-the-response"></a>Send a search request and receive the response 

1. Maak een functie met de naam `VideoSearch()` die uw abonnementssleutel als een tekenreeks neemt. Instantieer de zoekclient die eerder is gemaakt.
    
    ```java
    public static void VideoSearch(String subscriptionKey){
        VideoSearchAPIImpl client = VideoSDK.getClient(subscriptionKey);
        //...
    }
    ```
2. Verzend binnen `VideoSearch()` een aanvraag voor het zoeken naar video's met behulp van de client, met `SwiftKey` als de zoekterm. Als de Video's zoeken-API een resultaat heeft geretourneerd, haalt u het eerste resultaat op en drukt u de id, naam en URL ervan af, samen met het totale aantal video's dat is geretourneerd. 
    
    ```java
    VideosInner videoResults = client.searchs().list("SwiftKey");

    if (videoResults == null){
        System.out.println("Didn't see any video result data..");
    }
    else{
        if (videoResults.value().size() > 0){
            VideoObject firstVideoResult = videoResults.value().get(0);

            System.out.println(String.format("Video result count: %d", videoResults.value().size()));
            System.out.println(String.format("First video id: %s", firstVideoResult.videoId()));
            System.out.println(String.format("First video name: %s", firstVideoResult.name()));
            System.out.println(String.format("First video url: %s", firstVideoResult.contentUrl()));
        }
        else{
            System.out.println("Couldn't find video results!");
        }
    }
    ```

3. Roep de zoekmethode op vanuit uw hoofdmethode.

    ```java
    public static void main(String[] args) {
        VideoSDK.VideoSearch("YOUR-SUBSCRIPTION-KEY");
    }
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app met één pagina maken](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Zie ook 

* [Wat is de Bing Video's zoeken-API?](../overview.md)
* [Voorbeelden voor Cognitive Services .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)