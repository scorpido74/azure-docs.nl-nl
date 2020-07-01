---
title: 'Snelstartgids: zoeken naar afbeeldingen met behulp van de Bing Image Search REST API en C #'
titleSuffix: Azure Cognitive Services
description: Gebruik deze snelstartgids om aanvragen voor het zoeken van afbeeldingen naar de REST API voor Bing Image Search te verzenden met C# en JSON-antwoorden te ontvangen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.openlocfilehash: f35b69a3ca44308a7d314f448bf35b81d7119228
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85603516"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-c"></a>Snelstartgids: zoeken naar afbeeldingen met behulp van de Bing Image Search REST API en C #

Gebruik deze Quick Start om te leren hoe u zoek aanvragen naar de Bing Afbeeldingen zoeken-API verzendt. Deze C#-toepassing verzendt een zoekquery naar de API en geeft de URL weer van de eerste afbeelding in de resultaten. Hoewel deze toepassing is geschreven in C#, is de API een betrouw bare webservice die compatibel is met de meeste programmeer talen.

De bron code voor dit voor beeld is beschikbaar [op github](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingImageSearchv7Quickstart.cs) met aanvullende fout afhandeling en annotaties.

## <a name="prerequisites"></a>Vereisten
* Een versie van [Visual Studio 2017 of hoger](https://www.visualstudio.com/downloads/).
* Het [JSON.net](https://www.newtonsoft.com/json) -Framework, beschikbaar als een NuGet-pakket.
* Als u Linux/MacOS gebruikt, kan deze toepassing worden uitgevoerd met behulp van [Mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Een project maken en initialiseren

1. Maak een nieuwe console oplossing met de naam `BingSearchApisQuickStart` in Visual Studio. Voeg vervolgens de volgende naam ruimten toe aan het hoofd code bestand:

    ```csharp
    using System;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    using Newtonsoft.Json.Linq;
    ```

2. Maak variabelen voor het API-eindpunt, uw abonnementssleutel en zoekterm. Voor `uriBase` kunt u het globale eind punt in de volgende code gebruiken of het [aangepaste subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) eindpunt gebruiken dat wordt weer gegeven in de Azure portal voor uw resource.

    ```csharp
    //...
    namespace BingSearchApisQuickstart
    {
        class Program
        {
        // Replace the this string with your valid access key.
        const string subscriptionKey = "enter your key here";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";
        const string searchTerm = "tropical ocean";
    //...
    ```

## <a name="create-a-struct-to-format-the-bing-image-search-response"></a>Een struct maken om de reactie van Bing Afbeeldingen zoeken op te maken

Definieer een `SearchResult` struct die de afbeeldings Zoek resultaten en JSON-header gegevens bevat.

```csharp
    namespace BingSearchApisQuickstart
    {
        class Program
        {
        //...
            struct SearchResult
            {
                public String jsonResult;
                public Dictionary<String, String> relevantHeaders;
            }
//...
```

## <a name="create-a-method-to-send-search-requests"></a>Een methode voor het verzenden van zoekopdrachten maken

Maak een methode `BingImageSearch` met de naam om de aanroep naar de API uit te voeren en stel het retour type in op de `SearchResult` struct die u eerder hebt gemaakt.

```csharp
//...
namespace BingSearchApisQuickstart
{
    //...
    class Program
    {
        //...
        static SearchResult BingImageSearch(string searchTerm)
        {
        }
//...
```

## <a name="create-and-handle-an-image-search-request"></a>Een zoekopdracht voor afbeeldingen maken en verwerken

`BingImageSearch`Voer de volgende stappen uit in de-methode:

1. Stel de URI voor de zoekopdracht samen. Maak de `SearchTerm` Zoek term op voordat u deze toevoegt aan de teken reeks.

    ```csharp
    static SearchResult BingImageSearch(string SearchTerm){

        var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(SearchTerm);
    //...
    ```

2. Verstuur de webaanvraag en haal de reactie op als een JSON-tekenreeks.

    ```csharp
    WebRequest request = WebRequest.Create(uriQuery);
    request.Headers["Ocp-Apim-Subscription-Key"] = subscriptionKey;
    HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
    string json = new StreamReader(response.GetResponseStream()).ReadToEnd();
    ```

3. Maak het Zoek resultaat object en extraheer de Bing HTTP-headers. Retour neer `searchResult` .

    ```csharp
    // Create the result object for return
    var searchResult = new SearchResult()
    {
        jsonResult = json,
        relevantHeaders = new Dictionary<String, String>()
    };

    // Extract Bing HTTP headers
    foreach (String header in response.Headers)
    {
        if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
            searchResult.relevantHeaders[header] = response.Headers[header];
    }
    return searchResult;
    ```

## <a name="process-and-view-the-response"></a>De reactie verwerken en weergeven

1. Roep in de hoofdmethode `BingImageSearch()` aan en sla het geretourneerde antwoord op. Deserialiseren de JSON vervolgens in een-object.

    ```csharp
    SearchResult result = BingImageSearch(searchTerm);
    //deserialize the JSON response from the Bing Image Search API
    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result.jsonResult);
    ```

2. Haal de eerste geretourneerde afbeelding op uit `jsonObj` en druk de titel en een URL naar de afbeelding af.
    ```csharp
    var firstJsonObj = jsonObj["value"][0];
    Console.WriteLine("Title for the first image result: " + firstJsonObj["name"]+"\n");
    //After running the application, copy the output URL into a browser to see the image.
    Console.WriteLine("URL for the first image result: " + firstJsonObj["webSearchUrl"]+"\n");
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

* [Wat is de Bing Afbeeldingen zoeken-API?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Online interactieve demo proberen](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)
* [Prijs informatie voor de Bing Zoeken-API's](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)
* [Documentatie voor Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Naslag voor Bing Afbeeldingen zoeken-API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
