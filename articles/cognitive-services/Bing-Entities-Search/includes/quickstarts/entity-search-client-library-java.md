---
title: Bing Entity Search Java client library quickstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: 8c987aa14e922573d01aa35fab609edf01e109b4
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "79136772"
---
Gebruik deze snelstart om te beginnen met zoeken naar entiteiten met de Bing Entity Search-clientbibliotheek voor Java. Hoewel Bing Entity Search een REST API heeft die compatibel is met de meeste programmeertalen, biedt de clientbibliotheek een eenvoudige manier om de service in uw toepassingen te integreren. De broncode voor dit voorbeeld is te vinden op [GitHub.](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingEntitySearch)

## <a name="prerequisites"></a>Vereisten

* De [Java Development Kit(JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)

* De bing-entiteitzoekende clientbibliotheek voor Java

Installeer de afhankelijkheden van de Bing Entity Search-clientbibliotheek met Maven, Gradle of een ander afhankelijkheidsbeheersysteem. Het Maven-POM-bestand vereist de declaratie:

```xml
<dependency>
  <groupId>com.microsoft.azure.cognitiveservices</groupId>
  <artifactId>azure-cognitiveservices-entitysearch</artifactId>
  <version>1.0.2</version>
</dependency>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Een project maken en initialiseren

1. Maak een nieuw Java-project in uw favoriete IDE of editor en importeer de volgende bibliotheken.

    ```java
    import com.microsoft.azure.cognitiveservices.entitysearch.*;
    import com.microsoft.azure.cognitiveservices.entitysearch.implementation.EntitySearchAPIImpl;
    import com.microsoft.azure.cognitiveservices.entitysearch.implementation.SearchResponseInner;
    import com.microsoft.rest.credentials.ServiceClientCredentials;
    import okhttp3.Interceptor;
    import okhttp3.OkHttpClient;
    import okhttp3.Request;
    import okhttp3.Response;
    
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.List;
    ```

2. Een variabele maken voor uw abonnementssleutel

    ```java
    String subscriptionKey = "your-key-here"
    ```

## <a name="create-a-search-client"></a>Een zoekclient maken

1. Implementeer de client `dominantEntityLookup`, die uw API-eindpunt en een exemplaar van de klasse `ServiceClientCredentials` vereist. U het algemene eindpunt hieronder gebruiken of het [aangepaste subdomeineindpunt](../../../../cognitive-services/cognitive-services-custom-subdomains.md) dat wordt weergegeven in de Azure-portal voor uw bron.

    ```java
    public static EntitySearchAPIImpl getClient(final String subscriptionKey) {
        return new EntitySearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    Ga als volgt te werk om `ServiceClientCredentials` te implementeren:

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
    
   2. Roep binnen `applyCredentialsFilter()``builder.addNetworkInterceptor()` aan. Maak een nieuw `Interceptor`-object en overschrijf de methode `intercept()` ervan om een `Chain`-interceptorobject te nemen.

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
## <a name="send-a-request-and-receive-a-response"></a>Een aanvraag verzenden en een antwoord ontvangen

1. Maak een nieuw exemplaar van de zoekclient met uw abonnementssleutel. gebruik `client.entities().search()` voor het verzenden van een zoekopdracht voor de zoekquery `satya nadella`, en ontvang een antwoord. 
    
    ```java
    EntitySearchAPIImpl client = getClient(subscriptionKey);
    SearchResponseInner entityData = client.entities().search(
            "satya nadella", null, null, null, null, null, null, "en-us", null, null, SafeSearch.STRICT, null);
    ```

1. Als er entiteiten worden geretourneerd, zet u deze om in een lijst. Doorloop de entiteiten en geef de dominante entiteit weer.

    ```java
    if (entityData.entities().value().size() > 0){
        // Find the entity that represents the dominant entity
        List<Thing> entries = entityData.entities().value();
        Thing dominateEntry = null;
        for(Thing thing : entries) {
            if(thing.entityPresentationInfo().entityScenario() == EntityScenario.DOMINANT_ENTITY) {
                System.out.println("\r\nSearched for \"Satya Nadella\" and found a dominant entity with this description:");
                System.out.println(thing.description());
                break;
            }
        }
    }
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app van één pagina maken](../../tutorial-bing-entities-search-single-page-app.md).

* [Wat is de Bing Entiteiten zoeken-API?](../../overview.md)
