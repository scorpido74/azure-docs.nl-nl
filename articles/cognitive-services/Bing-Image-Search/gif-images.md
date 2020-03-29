---
title: Zoeken naar GIF-afbeeldingen met de Bing Image Search API
titleSuffix: Azure Cognitive Services
description: Met de Bing Image Search API u ook op het hele web zoeken naar de meest relevante .gif-afbeeldingen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: ad33b5a28728a8e7f9c0a70ff81ea5a9e65624bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072642"
---
# <a name="search-for-gif-images"></a>Gif-afbeeldingen zoeken 

Met de Bing Image Search API u ook op het hele web zoeken naar de meest relevante .gif-afbeeldingen.Ontwikkelaars kunnen boeiende gifs integreren in verschillende gespreksscenario's. 

De volgende URL is een query voor geanimeerde .gif-afbeeldingen.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us
```
De [q-parameter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) geeft de zoektermen op.  De vorige query `animatedGif` geeft ook aan met de parameter [imageType-filter.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype)

Als u voorbeelden van resultaten wilt zien, gebruikt u de volgende URL om bing.com te zoeken.
```
https://www.bing.com/images/search?q=interesting&qft=%20filterui%3Aphoto-animatedgif

```
## <a name="query-parameters"></a>Queryparameters

Zie de [referentie voor de API voor image search](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query-parameters)voor meer informatie over queryparameters en -opties. Een voorbeeld volgt onder het kopje [Voorbeeld zoeken naar geanimeerde gif met behulp van Java](#gifExample).

## <a name="tips-and-suggestions"></a>Tips en suggesties

- U de parameters [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) en [minFileSize](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize) opgeven. We raden u aan de maxFileSize=2000000 in te stellen omdat de meeste gifs in onze index onder de 2 MB vallen.  Dit helpt ook om de grootte van de gegevens te controleren als bandbreedte een probleem is, zoals in mobiele cellulaire scenario's.
- Laad de miniatuur eerst voordat u de bronurl laadt om de waargenomen prestaties te verbeteren.  
- Voor first-run of landing page ervaring waar je nog geen gebruikersquery hebt, probeer dan onze trending gif-zoekopdrachten te gebruiken om te helpen vanuit de [trending images API.](trending-images.md)
- Er zijn drie instellingen voor de [parameter safeSearch.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch)  De `strict` optie blokkeert inhoud voor volwassenen.
- Zie [mkt](supported-countries-markets.md) voor de volledige lijst met ondersteunde talen en locaties.
- *AnimatedGifHttps* retourneert alleen geanimeerde gifafbeeldingen die afkomstig zijn van een https-adres. Voor beveiliging vereisen veel toepassingen verbinding met externe webkoppelingen via https. De Apple App Store vereist bijvoorbeeld verbinding met webservices via HTTPS, waarmee gebruikersgegevens tijdens het transport veilig worden versleuteld.

<a name="gifExample" />

## <a name="example-search-for-animated-gif-using-java"></a>Voorbeeld zoeken naar geanimeerde gif met Java

In de volgende URL wordt gezocht naar geanimeerde .gif-afbeeldingen:`q=interesting`
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us

```
Zoals in het volgende voorbeeld wordt weergegeven, vereist de [URL-query de header Ocp-Apim-Subscription-Key.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#headers)

In het volgende Java-voorbeeld wordt het verzoek gebouwd en verzendt.

```
package gifSearch;
import java.net.*;
import java.util.*;
import java.io.*;
import javax.net.ssl.HttpsURLConnection;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac GIFsearch.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar GIFsearch
 */


public class GIFsearch {

    // Replace the subscriptionKey string value with your valid subscription key.
    static String subscriptionKey = "YOUR-ACCESS-KEY";

    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/images/search";

    static String searchTerm = "interesting";

    public static SearchResults SearchImages (String searchQuery) throws Exception {
        // construct URL of search request (endpoint + query string)
        URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "&imageType=AnimatedGif&mkt=en-us");
        HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

        // receive JSON body
        InputStream stream = connection.getInputStream();
        String response = new Scanner(stream).useDelimiter("\\A").next();

        // construct result object for return
        SearchResults results = new SearchResults(new HashMap<String, String>(), response);

        // extract Bing-related HTTP headers
        Map<String, List<String>> headers = connection.getHeaderFields();
        for (String header : headers.keySet()) {
            if (header == null) continue;      // may have null key
            if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
                results.relevantHeaders.put(header, headers.get(header).get(0));
            }
        }

        stream.close();
        return results;
    }

    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main (String[] args) {
        if (subscriptionKey.length() != 32) {
            System.out.println("Invalid Bing Search API subscription key!");
            System.out.println("Please paste yours into the source code.");
            System.exit(1);
        }

        try {
            System.out.println("Searching the Web for: " + searchTerm);

            SearchResults result = SearchImages(searchTerm);

            System.out.println("\nRelevant HTTP Headers:\n");
            for (String header : result.relevantHeaders.keySet())
                System.out.println(header + ": " + result.relevantHeaders.get(header));

            System.out.println("\nJSON Response:\n");
            System.out.println(prettify(result.jsonResponse));
        }
        catch (Exception e) {
            e.printStackTrace(System.out);
            System.exit(1);
        }
    }

}

//Container class for search results encapsulates relevant headers and JSON data
class SearchResults{
 HashMap<String, String> relevantHeaders;
 String jsonResponse;
 SearchResults(HashMap<String, String> headers, String json) {
     relevantHeaders = headers;
     jsonResponse = json;
 }
}

```

## <a name="results"></a>Resultaten
De code krijgt de volgende resultaten als JSON-objecten:

```json
    {
      "webSearchUrl": "https://www.bing.com/images/search?view\u003ddetai...",
      "name": "Very Interesting GIF - Thats Very Interesting - ...",
      "thumbnailUrl": "https://tse1.mm.bing.net/th?id\u003dOIP.yJX6Vz345JPK...",
      "datePublished": "2017-03-12T01:35:00.0000000Z",
      "contentUrl": "https://media.contoso.co/images/c895fa573df8e493ca8d0dec7d93b/raw",
      "hostPageUrl": "https://www.contoso.co/view/thats-very-interesting-christi...",
      "contentSize": "1295633 B",
      "encodingFormat": "animatedgif",
      "hostPageDisplayUrl": "https://www.contoso.co/view/thats-very-christian...",
      "width": 440,
      "height": 186,
      "thumbnail": {
        "width": 474,
        "height": 200
      },
      "imageInsightsToken": "ccid_yJX6Vz34*mid_9FF0FFA42AADA1357F042443D2103B40EA...",
      "insightsMetadata": {
        "recipeSourcesCount": 0,
        "bestRepresentativeQuery": {
          "text": "That\u0027s Very Interesting",
          "displayText": "That\u0027s Very Interesting",
          "webSearchUrl": "https://www.bing.com/images/search?q\u003dThat..."
        },
        "pagesIncludingCount": 19,
        "availableSizesCount": 2
      },
      "imageId": "9FF0FFA42AADA1357F042443D21030EAAA225F",
      "accentColor": "62452D"
    },

```

## <a name="next-steps"></a>Volgende stappen
- [Snelstart voor C#](quickstarts/csharp.md)
- [Toepassing met alleengaande afbeelding zoeken met één pagina](tutorial-bing-image-search-single-page-app.md)
