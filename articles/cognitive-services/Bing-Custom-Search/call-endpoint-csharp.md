---
title: 'Snelstartgids: uw Bing Aangepaste zoekopdrachten-eind punt aanroepen met C# | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Gebruik deze quickstart om te beginnen met het opvragen van zoekresultaten van uw Bing Custom Search-exemplaar in C#.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.openlocfilehash: e1084c1962db3c04b951245361da80bee098329a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199822"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>Snelstartgids: uw Bing Aangepaste zoekopdrachten-eind punt aanroepen met C # 

Gebruik deze Quick Start om te leren hoe u zoek resultaten kunt aanvragen van uw Bing Aangepaste zoekopdrachten-exemplaar. Hoewel deze toepassing is geschreven in C#, is de Bing Custom Search-API een REST-webservice die compatibel is met de meeste programmeer talen. De bron code voor dit voor beeld is beschikbaar op [github](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs).

## <a name="prerequisites"></a>Vereisten

- Een Bing Custom Search-exemplaar. Zie [Quick Start: uw eerste Bing aangepaste zoekopdrachten-exemplaar maken](quick-start.md)voor meer informatie.
- [Microsoft .net kern](https://www.microsoft.com/net/download/core).
- Een versie van [Visual Studio 2019 of hoger](https://www.visualstudio.com/downloads/).
- Als u Linux/MacOS gebruikt, kan deze toepassing worden uitgevoerd met behulp van [Mono](https://www.mono-project.com/).
- Het NuGet-pakket van [Bing aangepaste zoekopdrachten](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/2.0.0) . 

   Dit pakket installeren in Visual Studio: 
     1. Klik met de rechter muisknop op uw project in **Solution Explorer**en selecteer vervolgens **NuGet-pakketten beheren**. 
     2. Zoek en selecteer *micro soft. Azure. CognitiveServices. Search. CustomSearch*en installeer vervolgens het pakket.

   Wanneer u het Bing Aangepaste zoekopdrachten NuGet-pakket installeert, installeert Visual Studio ook de volgende pakketten:
     - **Microsoft.Rest.ClientRuntime**
     - **Micro soft. rest. ClientRuntime. Azure**
     - **Newtonsoft.Json**


[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak in Visual Studio een nieuwe C#-consoletoepassing. Voeg vervolgens de volgende pakketten toe aan uw project:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. Maak de volgende klassen om de zoek resultaten op te slaan die worden geretourneerd door de Bing Custom Search-API:

    ```csharp
    public class BingCustomSearchResponse {        
        public string _type{ get; set; }            
        public WebPages webPages { get; set; }
    }

    public class WebPages {
        public string webSearchUrl { get; set; }
        public int totalEstimatedMatches { get; set; }
        public WebPage[] value { get; set; }        
    }

    public class WebPage {
        public string name { get; set; }
        public string url { get; set; }
        public string displayUrl { get; set; }
        public string snippet { get; set; }
        public DateTime dateLastCrawled { get; set; }
        public string cachedPageUrl { get; set; }
    }
    ```

3. Maak in de hoofd methode van uw project de volgende variabelen voor uw Bing Custom Search-API-abonnements sleutel, de aangepaste configuratie-ID van het exemplaar en de zoek term:

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. Maak de aanvraag-URL door uw zoek term toe te voegen aan de `q=` query parameter en de aangepaste configuratie-id van uw Zoek instantie te koppelen aan de `customconfig=` para meter. Scheid de para meters met een en-teken ( `&` ). Voor de `url` waarde van de variabele kunt u het globale eind punt in de volgende code gebruiken of het [aangepaste subdomein](../../cognitive-services/cognitive-services-custom-subdomains.md) eindpunt gebruiken dat wordt weer gegeven in de Azure portal voor uw resource.

    ```csharp
    var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                "q=" + searchTerm + "&" +
                "customconfig=" + customConfigId;
    ```

## <a name="send-and-receive-a-search-request"></a>Een zoekaanvraag verzenden en ontvangen 

1. Maak een aanvraag-client, en voeg uw abonnementssleutel toe aan de `Ocp-Apim-Subscription-Key`-header.

    ```csharp
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    ```

2. Voer de zoekaanvraag uit en haal de reactie op als een JSON-object.

    ```csharp
    var httpResponseMessage = client.GetAsync(url).Result;
    var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
    BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
    ```
## <a name="process-and-view-the-results"></a>De resultaten verwerken en weergeven

- Doorloop het reactieobject om informatie weer te geven over elk zoekresultaat, waaronder de naam, de URL en de datum waarop de webpagina voor het laatst is gecrawld.

    ```csharp
    for(int i = 0; i < response.webPages.value.Length; i++) {                
        var webPage = response.webPages.value[i];
        
        Console.WriteLine("name: " + webPage.name);
        Console.WriteLine("url: " + webPage.url);                
        Console.WriteLine("displayUrl: " + webPage.displayUrl);
        Console.WriteLine("snippet: " + webPage.snippet);
        Console.WriteLine("dateLastCrawled: " + webPage.dateLastCrawled);
        Console.WriteLine();
    }
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app voor aangepaste zoekopdrachten bouwen](./tutorials/custom-search-web-page.md)
