---
title: Snelstart - Een query naar de API verzenden met Java - Bing Local Business Search
titleSuffix: Azure Cognitive Services
description: Gebruik deze snelstart om aanvragen te verzenden naar de Bing Local Business Search API, een Azure Cognitive Service.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: 8ff70bea8d0e4810b6d5a0d35853077ed0a630cd
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74665182"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-java"></a>Snelstart: een query verzenden naar de Bing Local Business Search API met Java

Gebruik deze snelstart om aanvragen te verzenden naar de Bing Local Business Search API, een Azure Cognitive Service. Hoewel deze eenvoudige toepassing is geschreven in Java, de API is een RESTful Web service compatibel met elke programmeertaal in staat van het maken van HTTP-verzoeken en ontleden JSON.

Met deze voorbeeldtoepassing worden lokale antwoordgegevens `hotel in Bellevue`uit de API voor de zoekopdracht opgehaald.

## <a name="prerequisites"></a>Vereisten

* De [Java Development Kit(JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

U moet een [API-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) voor Cognitive Services hebben met Bing Search API's. De [gratis proefversie](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) is voldoende voor deze quickstart. U hebt de toegangssleutel nodig die is verstrekt tijdens het activeren van uw gratis proefversie.  Zie ook [Prijsinformatie Cognitive Services - Bing Zoeken-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

In deze voorbeeldtoepassing worden lokale antwoordgegevens opgehaald van de query voor een *hotel in Bellevue.*

## <a name="create-the-request"></a>De aanvraag maken 

Met de volgende `WebRequest`code wordt een , hiermee wordt de koptekst van de toegangstoets ingesteld en wordt een querytekenreeks voor 'hotel in Bellevue' toegevoegd.  Vervolgens wordt de aanvraag verzonden en het antwoord toegewezen aan een tekenreeks die de JSON-tekst moet bevatten.

```java
    // construct URL of search request (endpoint + query string)
     URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + &mkt=en-us");
    HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

    // receive JSON body
    InputStream stream = connection.getInputStream();
    String response = new Scanner(stream).useDelimiter("\\A").next();

    // construct result object for return
    SearchResults results = new SearchResults(new HashMap<String, String>(), response);
```

## <a name="run-the-complete-application"></a>De volledige toepassing uitvoeren

De Bing Local Business Search API retourneert resultaten van de Bing-zoekmachine.
1. Download of installeer de gson-bibliotheek.
2. Maak een nieuw Java-project in uw favoriete IDE of editor.
3. Voeg de onderstaande code toe.
4. Vervang de waarde van subscriptionKey door een geldige toegangssleutel voor uw abonnement.
5. Voer het programma uit.

```java
package localSearch;
import java.net.*;
import java.util.*;
import java.io.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (localSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac localSearch.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar localSearch
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

public class LocalSearchCls {

    // ***********************************************
    // *** Update or verify the following values. ***
    // **********************************************

        // Replace the subscriptionKey string value with your valid subscription key.
        static String subscriptionKey = "YOUR-ACCESS-KEY";

        static String host = "https://api.cognitive.microsoft.com/bing";
        static String path = "/v7.0/localbusinesses/search";

        static String searchTerm = "Hotel in Bellevue";

        public static SearchResults SearchLocal (String searchQuery) throws Exception {
            // construct URL of search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "&mkt=en-us");
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
            try {
                System.out.println("Searching the Web for: " + searchTerm);

                SearchResults result = SearchLocal(searchTerm);

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

    // Container class for search results encapsulates relevant headers and JSON data
    class SearchResults{
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    }

```

## <a name="next-steps"></a>Volgende stappen
- [Zoeken in lokaal bedrijf snel gestart](local-quickstart.md)
- [Lokaal zakelijk zoekknooppunt snel aan de slag](local-search-node-quickstart.md)
- [Lokaal zoeken in Business Python snel gestart](local-search-python-quickstart.md)
