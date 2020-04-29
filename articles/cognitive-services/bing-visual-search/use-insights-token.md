---
title: Insights-token-Bing Visual Search gebruiken
titleSuffix: Azure Cognitive Services
description: Laat zien hoe u het Insight-token van een afbeelding gebruikt met Bing Visual Search-API om inzicht te krijgen in een afbeelding.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 4/26/2019
ms.author: scottwhi
ms.openlocfilehash: 251197c456ece4fe2dbbe264219d52f3502b7492
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67341722"
---
# <a name="use-an-insights-token-to-get-insights-for-an-image"></a>Een Insights-token gebruiken om inzichten op te halen voor een installatie kopie

Bing Visual Search-API retourneert informatie over een afbeelding die u opgeeft. U kunt de afbeelding opgeven door de URL van de afbeelding te gebruiken, een inzichttoken of door een afbeelding te uploaden. Zie [Wat is Bing Visual Search-API?](overview.md)voor meer informatie over deze opties. In dit artikel wordt gedemonstreerd hoe u een Insights-token gebruikt. Voor voor beelden van hoe u een afbeelding uploadt om inzicht te krijgen, raadpleegt u de Quick starts ([C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node. js](quickstarts/nodejs.md) | [python](quickstarts/python.md)).

Als u Bing Visual Search een afbeeldings token of URL verzendt, worden de formulier gegevens weer gegeven die u moet gebruiken in de hoofd tekst van het bericht. De formulier gegevens moeten de `Content-Disposition` koptekst bevatten en u moet de `name` para meter instellen op ' knowledgeRequest '. Voor meer informatie over `imageInfo` het object raadpleegt u de aanvraag:

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

In de voor beelden in dit artikel ziet u hoe u het Insights-token gebruikt. U krijgt het Insights-token van `Image` een object in een/Images/Search API-antwoord. Zie [Wat is het Bing afbeeldingen zoeken-API?](../Bing-Image-Search/overview.md)voor informatie over het verkrijgen van het Insights-token.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "ccid_tmaGQ2eU*mid_D12339146CFEDF3D40...",
    }
}

--boundary_1234-abcd--
```

Zie [C#](#use-with-c) | [Java](#use-with-java) | [node. js](#use-with-nodejs) | [python](#use-with-python)voor voor beelden die gebruikmaken van het Insights-token.

## <a name="use-with-c"></a>Gebruiken met C #

### <a name="c-prerequisites"></a>C#-vereisten

- Een versie van [Visual Studio 2019](https://www.visualstudio.com/downloads/) voor het verkrijgen van deze code die wordt uitgevoerd in Windows.
- Een Azure-abonnement. U kunt voor deze Quick Start een [gratis proef](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) abonnement of een betaalde abonnements sleutel gebruiken.

## <a name="run-the-application"></a>De toepassing uitvoeren

Volg deze stappen voor het uitvoeren van deze toepassing:

1. Maak een console oplossing in Visual Studio.
2. Vervang de inhoud van Program.cs door de code die in deze Quick Start wordt weer gegeven.
3. Vervang de waarde `accessKey` door uw abonnementscode.
4. Vervang de `insightsToken` waarde door een Insights-token van een/Images/Search-antwoord.
5. Voer het programma uit.

```csharp
using System;
using System.Text;
using System.Net;
using System.IO;
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using System.Threading;

namespace VisualSearchInsightsToken
{

    class Program
    {
        // Replace the accessKey string value with your valid subscription key.
        const string accessKey = "<yoursubscriptionkeygoeshere>";

        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";

        // Update with an insights token from an image object that the /images/search API endpoint returns.
        static string insightsToken = @"ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

        static string BoundaryTemplate = "batch_{0}";

        static void Main(string[] args)
        {
            try { 
                Console.WriteLine("Getting image insights using insights token: " + insightsToken);

                var knowledgeRequest = "{\"imageInfo\" : {\"imageInsightsToken\" : \"" + insightsToken + "\"}}";
                var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());

                var json = BingVisualSearch(knowledgeRequest, boundary, uriBase, accessKey);

                Console.WriteLine("\nJSON Response:\n");
                Console.WriteLine(JsonPrettyPrint(json));

                Console.Write("\nPress Enter to exit ");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }

        }


        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response with insights.
        /// </summary>
        static string BingVisualSearch(string knowledgeRequest, string boundary, string uri, string subscriptionKey)
        {
            var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
            requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

            var content = new MultipartFormDataContent(boundary);
            content.Add(new StringContent(knowledgeRequest, Encoding.UTF8, "application/json"), "knowledgeRequest");
            requestMessage.Content = content;

            var httpClient = new HttpClient();

            Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
            HttpResponseMessage httpResponse = httpRequest.Result;
            HttpStatusCode statusCode = httpResponse.StatusCode;
            HttpContent responseContent = httpResponse.Content;

            string json = null;

            if (responseContent != null)
            {
                Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                json = stringContentsTask.Result;
            }


            return json;
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
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
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
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }
    }
}
```

## <a name="use-with-java"></a>Gebruiken met Java

### <a name="java-prerequisites"></a>Java-vereisten

- U moet [JDK 7 of 8](https://aka.ms/azure-jdks) gebruiken om deze code te compileren en uit te voeren. U kunt een Java IDE gebruiken als u een favoriet hebt, maar een tekst editor is voldoende.
- U kunt voor deze Quick Start een [gratis proef](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) abonnement of een betaalde abonnements sleutel gebruiken.

## <a name="run-the-java-application"></a>De Java-toepassing uitvoeren

Volg deze stappen voor het uitvoeren van deze toepassing:

1. Down load of installeer de [Gson Java-bibliotheek](https://github.com/google/gson). U kunt ook Gson verkrijgen via maven.
2. Maak een nieuw Java-project in uw favoriete IDE of editor.
3. Voeg de geleverde code toe aan een bestand met de naam `VisualSearch.java`.
4. Vervang de waarde `subscriptionKey` door uw abonnementscode.
5. Voer het programma uit.

```java
package insightstoken;

import java.util.*;
import java.io.*;



/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.2
 *
 * Once you have compiled or downloaded gson-2.8.2.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac BingImageSearch.java -classpath .;gson-2.8.2.jar -encoding UTF-8
 * java -cp .;gson-2.8.2.jar BingImageSearch
 */

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

// https://hc.apache.org/downloads.cgi (HttpComponents Downloads) HttpClient 4.5.5

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.ContentType;
import org.apache.http.entity.mime.MultipartEntityBuilder;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClientBuilder;


public class InsightsToken {

    
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "<yoursubscriptionkeygoeshere>";

    // To get an insights, call the /images/search endpoint. Get the token from
    // the imageInsightsToken field in the Image object.
    static String insightsToken = "ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();
        
        String knowledgeRequest = "{\"imageInfo\" : {\"imageInsightsToken\" : \"" + insightsToken + "\"}}";

        try {
            HttpEntity entity = MultipartEntityBuilder
                .create()
                .addTextBody("knowledgeRequest", knowledgeRequest, ContentType.create("application/json"))
                .build();

            HttpPost httpPost = new HttpPost(endpoint);
            httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            httpPost.setEntity(entity);
            HttpResponse response = httpClient.execute(httpPost);

            InputStream stream = response.getEntity().getContent();
            String json = new Scanner(stream).useDelimiter("\\A").next();

            System.out.println("\nJSON Response:\n");
            System.out.println(prettify(json));
        }
        catch (IOException e)
        {
            e.printStackTrace(System.out);
            System.exit(1);
        }
        catch (Exception e) {
            e.printStackTrace(System.out);
            System.exit(1);
        }
    }
    
    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }


}
```

## <a name="use-with-nodejs"></a>Gebruiken met node. js

### <a name="nodejs-prerequisites"></a>Vereisten voor node. js

- U moet [node. js 6](https://nodejs.org/en/download/) hebben om deze code uit te voeren.
- U kunt voor deze Quick Start een [gratis proef](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) abonnement of een betaalde abonnements sleutel gebruiken.

## <a name="run-the-javascript-application"></a>De Java script-toepassing uitvoeren

Volg deze stappen voor het uitvoeren van deze toepassing:

1. Maak een map voor het project (of gebruik uw favoriete IDE of editor).
2. Navigeer vanuit een opdrachtprompt of terminal naar de map die u zojuist hebt gemaakt.
3. Installeer de aanvraagmodules:
  
   ```
   npm install request  
   ```
1. Installeer de formuliergegevensmodules:  
   ```
   npm install form-data  
   ```
1. Maak een bestand met de naam GetVisualInsights.js en voeg de volgende code toe.
1. Vervang de waarde `subscriptionKey` door uw abonnementscode.
1. Voer het programma uit.  
   ```
   node GetVisualInsights.js
   ```

```javascript
var request = require('request');
var FormData = require('form-data');

var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
var subscriptionKey = '<yoursubscriptionkeygoeshere>';

// To get an insights, call the /images/search endpoint. Get the token from
// the imageInsightsToken field in the Image object.
var insightsToken = "ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

var knowledgeRequest = {
    "imageInfo" : {
        "imageInsightsToken" : insightsToken
    }
};

var form = new FormData();
form.append('knowledgeRequest', JSON.stringify(knowledgeRequest));

form.getLength(function(err, length){
  if (err) {
    return requestCallback(err);
  }

  var r = request.post(baseUri, requestCallback);
  r._form = form; 
  r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
});

function requestCallback(err, res, body) {
    console.log(JSON.stringify(JSON.parse(body), null, '  '))
}
```

## <a name="use-with-python"></a>Gebruiken met python

### <a name="python-prerequisites"></a>Vereisten voor python

- U moet [python 3](https://www.python.org/) hebben om deze code uit te voeren.
- In deze Quick Start kunt u een abonnementssleutel van een [gratis proefversie](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) of een betaalde abonnementssleutel gebruiken.

## <a name="run-the-python-application"></a>De python-toepassing uitvoeren

Volg deze stappen voor het uitvoeren van deze toepassing:

1. Maak een nieuw Python-project in uw favoriete IDE of editor.
2. Maak een bestand met de naam visualsearch.py en voeg de code toe die wordt weergegeven in deze snelstart.
3. Vervang de waarde `SUBSCRIPTION_KEY` door uw abonnementscode.
4. Voer het programma uit.

```python
"""Bing Visual Search example"""

# Download and install Python at https://www.python.org/
# Run the following in a command console window
# pip3 install requests

import requests
import json

BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'

SUBSCRIPTION_KEY = '<yoursubscriptionkeygoeshere>'

HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}

# To get an insights, call the /images/search endpoint. Get the token from
# the imageInsightsToken field in the Image object.
insightsToken = 'ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ'

formData = '{"imageInfo":{"imageInsightsToken":"' + insightsToken + '"}}'


file = {'knowledgeRequest': (None, formData)}


def main():

    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())

    except Exception as ex:
        raise ex


def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))


# Main execution
if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>Volgende stappen

[Een Visual Search Web-app met één pagina maken](tutorial-bing-visual-search-single-page-app.md)  
[Wat is Bing Visual Search-API?](overview.md)  
[Cognitive Services proberen](https://aka.ms/bingvisualsearchtryforfree)  
[Een toegangscode voor een gratis proefversie aanvragen](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Afbeeldingen-Visual Search](https://aka.ms/bingvisualsearchreferencedoc)
