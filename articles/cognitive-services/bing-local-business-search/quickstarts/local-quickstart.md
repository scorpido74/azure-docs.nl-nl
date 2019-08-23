---
title: 'Quick Start: een query verzenden naar de Bing lokale Business Search-API inC#'
titleSuffix: Azure Cognitive Services
description: Gebruik dit artikel om te beginnen met het gebruik van de Bing lokale C#zakelijke zoek-API in.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 04/26/2019
ms.author: rosh
ms.openlocfilehash: 163a85a7fb7521054ebec58fb9ac86373963f2b9
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906313"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-c"></a>Quickstart: Een query naar de Bing Local Business Search-API verzenden inC#

Gebruik deze Quick Start om aanvragen te verzenden naar de Bing lokale Business Search-API, een Azure-cognitieve service. Hoewel deze eenvoudige toepassing is geschreven in C#, is de API een betrouw bare webservice die compatibel is met elke programmeer taal die kan leiden tot het maken van HTTP-aanvragen en het parseren van JSON.

In deze voorbeeld toepassing worden lokale antwoord gegevens opgehaald uit de API voor de `hotel in Bellevue`Zoek query.

## <a name="prerequisites"></a>Vereisten

* Alle edities van [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Als u Linux/MacOS gebruikt, kan deze toepassing worden uitgevoerd met behulp van [Mono](https://www.mono-project.com/).

U moet beschikken over een [account voor de Cognitive Services-API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) met Bing Zoeken-API's. De [gratis proefversie](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) is voldoende voor deze snelstartgids.  Zie ook [Prijsinformatie Cognitive Services - Bing Zoeken-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-the-request"></a>De aanvraag maken 

Met de volgende code maakt `WebRequest`u een, stelt de kop van de toegangs sleutel in en voegt een query reeks toe voor ' restaurant in Bellevue '.  Vervolgens wordt de aanvraag verzonden en het antwoord toegewezen aan een tekenreeks die de JSON-tekst moet bevatten.

```
    // Replace the accessKey string value with your valid access key.
    const string accessKey = "enter key here";

    const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search";   

    const string searchTerm = "restaurant in Bellevue";
    // Construct the URI of the search request
    var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery) + mkt=en-us;

    // Run the Web request and get response.
    WebRequest request = HttpWebRequest.Create(uriQuery);
    request.Headers["Ocp-Apim-Subscription-Key"] = accessKey; 

    HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
    string json = new StreamReader(response.GetResponseStream()).ReadToEnd();
```

## <a name="run-the-complete-application"></a>De volledige toepassing uitvoeren

De Bing Local Business Search-API retourneert gelokaliseerde Zoek resultaten van de Bing zoek machine.
1. Maak een nieuwe Console-oplossing in Visual Studio (de Community Edition volstaat).
2. Vervang Program.cs door de code hieronder.
3. Vervang de waarde van accessKey door een toegangs sleutel die geldig is voor uw abonnement.
4. Voer het programma uit.

```
using System;
using System.Collections.Generic;
using System.Text;
using System.Net;
using System.IO;

namespace localSearch
{
    class Program
    {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        // Replace the accessKey string value with your valid access key.
        const string accessKey = "enter key here";

        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search";   

        const string searchTerm = "restaurant in Bellevue";

        // Returns search results including relevant headers
        struct SearchResult
        {
            public String jsonResult;
            public Dictionary<String, String> relevantHeaders;
        }

        static void Main()
        {
            Console.OutputEncoding = System.Text.Encoding.UTF8;
            Console.WriteLine("Searching locally for: " + searchTerm);

            SearchResult result = BingLocalSearch(searchTerm, accessKey);

            Console.WriteLine("\nRelevant HTTP Headers:\n");
            foreach (var header in result.relevantHeaders)
                Console.WriteLine(header.Key + ": " + header.Value);

            Console.WriteLine("\nJSON Response:\n");
            Console.WriteLine(JsonPrettyPrint(result.jsonResult));

            Console.Write("\nPress Enter to exit ");
            Console.ReadLine();
        }

        /// <summary>
        /// Performs a Bing Local business search and return the results as a SearchResult.
        /// </summary>
        static SearchResult BingLocalSearch(string searchQuery, string key)
        {
            // Construct the URI of the search request
            var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery) + "&mkt=en-us";

            // Perform the Web request and get the response
            WebRequest request = HttpWebRequest.Create(uriQuery);
            request.Headers["Ocp-Apim-Subscription-Key"] = key; 

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            // Create result object for return
            var searchResult = new SearchResult();
            searchResult.jsonResult = json;
            searchResult.relevantHeaders = new Dictionary<String, String>();

            // Extract Bing HTTP headers
            foreach (String header in response.Headers)
            {
                if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
                    searchResult.relevantHeaders[header] = response.Headers[header];
            }

            return searchResult;
        }

        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            int offset = 0;
            int indentLength = 3;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\'':
                        if (quote) ignore = !ignore;
                        break;
                }

                if (quote)
                    sb.Append(ch);
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (ch != ' ') sb.Append(ch);
                            break;
                    }
                }
            }

            return sb.ToString().Trim();
        }
    }
}

```

## <a name="next-steps"></a>Volgende stappen
- [Lokale zakelijke zoek opdracht voor zoeken in Java Quick Start](local-search-java-quickstart.md)
- [Quick start voor lokaal bedrijfs Zoek knooppunt](local-search-node-quickstart.md)
- [Snelstartgids voor lokale zakelijke Zoek opdrachten python](local-search-python-quickstart.md)
