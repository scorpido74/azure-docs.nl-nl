---
title: 'Quickstart: Java gebruiken om de Bing Image Search aan te roepen REST API'
titleSuffix: Azure Cognitive Services
description: Gebruik deze snelstartgids om aanvragen voor het zoeken van afbeeldingen naar de REST API voor Bing Image Search te verzenden met Java en JSON-antwoorden te ontvangen.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 07/22/2019
ms.author: aahi
ms.custom: seodec2018, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 415613d99b3a7b9f3845a458886e93e5655842c8
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899698"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-api-an-azure-cognitive-service"></a>Quickstart: Afbeeldingen zoeken met de Bing Afbeeldingen zoeken-API, een Azure cognitieve service 

Gebruik deze Quick Start om Zoek aanvragen te verzenden naar de Bing Afbeeldingen zoeken-API in azure Cognitive Services. Deze Java-toepassing verzendt een zoekquery naar de API en geeft de URL weer van de eerste afbeelding in de resultaten. Hoewel deze toepassing in Java is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal.

De broncode voor dit voorbeeld is beschikbaar op [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingImageSearchv7Quickstart.java) met extra foutafhandeling en aantekeningen.

## <a name="prerequisites"></a>Vereisten

* De [Java Development Kit(JDK)](https://aka.ms/azure-jdks)

* De [Gson-bibliotheek](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Een project maken en initialiseren

1. Maak een nieuw Java-project in uw favoriete IDE of editor en importeer de volgende bibliotheken.

    ```java
    import java.net.*;
    import java.util.*;
    import java.io.*;
    import javax.net.ssl.HttpsURLConnection;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    ```

2. Maak variabelen voor het API-eindpunt, uw abonnementssleutel en zoekterm.

    ```java
    static String subscriptionKey = "enter key here";
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/images/search";
    static String searchTerm = "tropical ocean";
    ```

## <a name="construct-the-search-request-and-query"></a>De zoekopdracht en query compileren

1. Gebruik de variabelen uit de laatste stap om een zoek-URL voor de API-aanvraag te formatteren. Houd er rekening mee dat uw zoekterm URL-gecodeerd moet zijn voordat deze wordt toegevoegd aan de aanvraag.

    ```java
    // construct the search request URL (in the form of endpoint + query string)
    URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8"));
    HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    ```

## <a name="receive-and-process-the-json-response"></a>Het JSON-antwoord ontvangen en verwerken

1. Ontvang het JSON-antwoord van de Bing Afbeeldingen zoeken-API, en bouw het resultaatobject.

    ```java
    // receive JSON body
    InputStream stream = connection.getInputStream();
    String response = new Scanner(stream).useDelimiter("\\A").next();
    // construct result object for return
    SearchResults results = new SearchResults(new HashMap<String, String>(), response);
    ```
2. De Bing-gerelateerde HTTP-headers scheiden van de JSON-hoofdtekst
    ```java
    // extract Bing-related HTTP headers
    Map<String, List<String>> headers = connection.getHeaderFields();
    for (String header : headers.keySet()) {
        if (header == null) continue;      // may have null key
        if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
            results.relevantHeaders.put(header, headers.get(header).get(0));
        }
    }
    ```

3. Sluit de stroom en parseer het antwoord. Haal het totale aantal geretourneerde zoekresultaten en de URL van de miniatuur op voor het eerste afbeeldingsresultaat.

    ```java
    stream.close();
    JsonParser parser = new JsonParser();
    JsonObject json = parser.parse(result.jsonResponse).getAsJsonObject();
    //get the first image result from the JSON object, along with the total
    //number of images returned by the Bing Image Search API.
    String total = json.get("totalEstimatedMatches").getAsString();
    JsonArray results = json.getAsJsonArray("value");
    JsonObject first_result = (JsonObject)results.get(0);
    String resultURL = first_result.get("thumbnailUrl").getAsString();
    ```

## <a name="example-json-response"></a>Voorbeeld van JSON-antwoord

Antwoorden die afkomstig zijn van de Bing Afbeeldingen zoeken-API, worden geretourneerd in de JSON-indeling. Dit voorbeeldantwoord is ingekort zodat één resultaat wordt weergegeven.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie voor app met één pagina voor Bing Image Search](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Zie ook

* [Wat is Bing Image Search?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Online interactieve demo proberen](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) 
* [Prijsinformatie](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) voor Bing Zoek-API's. 
* [Gratis Cognitive Services-toegangssleutel ophalen](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Documentatie van Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Naslag voor Bing Afbeeldingen zoeken-API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
