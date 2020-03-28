---
title: 'Snelstart: krijg beeldinzichten met behulp van de REST API en Java - Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Leer hoe u een afbeelding uploadt naar de Bing Visual Search-API en inzichten in de afbeelding verkrijgt.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: fe323fc27062ad1bee9abdfaf3408430e28523a9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446621"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-java"></a>Snelstart: krijg beeldinzichten met behulp van de Bing Visual Search REST API en Java

Gebruik deze snelstart om uw eerste gesprek te voeren naar de Bing Visual Search API en de resultaten weer te geven. Deze Java-toepassing uploadt een afbeelding naar de API en geeft de informatie weer die wordt geretourneerd. Hoewel deze toepassing is geschreven in Java, de API is een RESTful Web service compatibel met de meeste programmeertalen.

## <a name="prerequisites"></a>Vereisten

* De [Java Development Kit (JDK) 7 of 8](https://aka.ms/azure-jdks)
* De [Gson Java bibliotheek](https://github.com/google/gson)
* [Apache HttpComponents](https://hc.apache.org/downloads.cgi)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Een project maken en initialiseren

1. Maak een nieuw Java-project in uw favoriete IDE of editor en importeer de volgende bibliotheken:

    ```java
    import java.util.*;
    import java.io.*;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    
    // HttpClient libraries
    
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.ContentType;
    import org.apache.http.entity.mime.MultipartEntityBuilder;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClientBuilder;
    ```

2. Maak variabelen voor uw API-eindpunt, abonnementssleutel en het pad naar uw afbeelding. `endpoint`kan het algemene eindpunt hieronder zijn of het [aangepaste eindpunt voor subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) dat wordt weergegeven in de Azure-portal voor uw bron:

    ```java
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "your-key-here";
    static String imagePath = "path-to-your-image";
    ```

    
    Wanneer u een lokale afbeelding uploadt, `Content-Disposition` moeten de formuliergegevens de koptekst bevatten. U moet `name` de parameter instellen op 'afbeelding' en u de `filename` parameter instellen op elke tekenreeks. De inhoud van het formulier bevat de binaire gegevens van de afbeelding. De maximale afbeeldingsgrootte die u uploaden is 1 MB.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

## <a name="create-the-json-parser"></a>De JSON-parser maken

Maak een methode om de JSON-respons van `JsonParser`de API leesbaarder te maken met :

```java
public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
```

## <a name="construct-the-search-request-and-query"></a>De zoekopdracht en query compileren

1. Maak in de hoofdmethode van uw `HttpClientBuilder.create().build();`toepassing een HTTP-client met :

    ```java
    CloseableHttpClient httpClient = HttpClientBuilder.create().build();
    ```

2. Maak `HttpEntity` een object om uw afbeelding naar de API te uploaden:

    ```java
    HttpEntity entity = MultipartEntityBuilder
        .create()
        .addBinaryBody("image", new File(imagePath))
        .build();
    ```

3. Maak `httpPost` een object met uw eindpunt en stel de koptekst in om uw abonnementssleutel te gebruiken:

    ```java
    HttpPost httpPost = new HttpPost(endpoint);
    httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
    httpPost.setEntity(entity);
    ```

## <a name="receive-and-process-the-json-response"></a>Het JSON-antwoord ontvangen en verwerken

1. Gebruik `HttpClient.execute()` de methode om een aanvraag naar de API `InputStream` te verzenden en sla het antwoord op in een object:
    
    ```java
    HttpResponse response = httpClient.execute(httpPost);
    InputStream stream = response.getEntity().getContent();
    ```

2. Sla de JSON-tekenreeks op en druk het antwoord af:

    ```java
    String json = new Scanner(stream).useDelimiter("\\A").next();
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(json));
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app voor visueel zoeken met één pagina maken](../tutorial-bing-visual-search-single-page-app.md)
