---
title: 'Quickstart: Een zoekaanvraag verzenden naar de REST API met C# - Bing Entity Search'
titleSuffix: Azure Cognitive Services
description: Gebruik deze quickstart om een aanvraag naar de Bing Entiteiten zoeken-REST API te verzenden via C# en een JSON-antwoord te ontvangen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 131c282ab61f04ffa94921c2816886a35ea4b7ac
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88936823"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-c"></a>Quickstart: Een zoekaanvraag naar de REST-API van Bing Entiteiten zoeken sturen met C#

Gebruik deze quickstart om voor het eerst de Bing Entiteiten zoeken-API aan te roepen en het JSON-antwoord te bekijken. Deze eenvoudige C#-toepassing stuurt een query naar de API om nieuws te zoeken en geeft het antwoord weer. De broncode voor deze toepassing is beschikbaar [op GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingEntitySearchv7.cs).

Hoewel deze toepassing is geschreven in C#, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal.


## <a name="prerequisites"></a>Vereisten

- Een versie van [Visual Studio 2017 of hoger](https://www.visualstudio.com/downloads/).

- Het [Json.NET](https://www.newtonsoft.com/json)-framework, beschikbaar als NuGet-pakket. U kunt als volgt het NuGet-pakket installeren in Visual Studio:

   1. Klik met de rechtermuisknop op uw project in **Solution Explorer**.
   2. Selecteer **NuGet-pakketten beheren**.
   3. Zoek en selecteer *Newtonsoft.Json* en installeer het pakket.

- Als u Linux/MacOS gebruikt, kan deze toepassing worden uitgevoerd met behulp van [Mono](https://www.mono-project.com/).


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Een project maken en initialiseren

1. Maak een nieuwe C#-console-oplossing in Visual Studio. Voeg de volgende naamruimten in het hoofdcodebestand in:
    
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    ```

2. Maak een nieuwe klasse en voeg variabelen toe voor het API-eindpunt, uw abonnementssleutel en de query waarop u wilt zoeken. U kunt het globale eindpunt in de volgende code gebruiken of het eindpunt voor een [aangepast subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) gebruiken dat wordt weergegeven in de Azure-portal voor uw resource.

    ```csharp
    namespace EntitySearchSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/entities";
    
            static string market = "en-US";
    
            // NOTE: Replace this example key with a valid subscription key.
            static string key = "ENTER YOUR KEY HERE";
    
            static string query = "italian restaurant near me";
        //...
        }
    }
    ```

## <a name="send-a-request-and-get-the-api-response"></a>Een aanvraag verzenden en het API-antwoord ontvangen

1. Maak in de klasse een functie met de naam `Search()`. Maak in deze functie een nieuw `HttpClient`-object en voeg uw abonnementssleutel toe aan de `Ocp-Apim-Subscription-Key`-header.

2. Maak de URI voor uw aanvraag door de host en het pad te combineren. Voeg vervolgens uw markt toe en pas URL-codering toe op uw query.

3. Wacht totdat `client.GetAsync()` een HTTP-antwoord krijgt en sla vervolgens het JSON-antwoord op door te wachten op `ReadAsStringAsync()`.

4. Maak de JSON-tekenreeks op met `JsonConvert.DeserializeObject()` en geef deze weer op de console.

      ```csharp
      async static void Search()
      {
       //...
       HttpClient client = new HttpClient();
       client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

       string uri = host + path + "?mkt=" + market + "&q=" + System.Net.WebUtility.UrlEncode(query);

       HttpResponseMessage response = await client.GetAsync(uri);

       string contentString = await response.Content.ReadAsStringAsync();
       dynamic parsedJson = JsonConvert.DeserializeObject(contentString);
       Console.WriteLine(parsedJson);
      }
      ```

5. Roep in de methode `Main()` van uw toepassing de functie `Search()` aan.
    
    ```csharp
    static void Main(string[] args)
    {
        Search();
        Console.ReadLine();
    }
    ```


## <a name="example-json-response"></a>Voorbeeld van JSON-antwoord

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app van één pagina maken](../tutorial-bing-entities-search-single-page-app.md).

* [Wat is de Bing Entiteiten zoeken-API?](../overview.md )
* [Naslaghandleiding Bing Entiteiten zoeken-API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).
