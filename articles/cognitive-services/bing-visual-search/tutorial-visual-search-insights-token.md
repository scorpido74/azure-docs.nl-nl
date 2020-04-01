---
title: Vergelijkbare afbeeldingen van eerdere zoekopdrachten zoeken met behulp van image insights-tokens en de Bing Visual Search API
titleSuffix: Azure Cognitive Services
description: Gebruik de clientbibliotheek Van Bing Visual Search om URL's van afbeeldingen uit eerdere zoekopdrachten te krijgen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: aahi
ms.openlocfilehash: ad24a8a194a11c3fd5f7f77ea8c52197d5438edc
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477923"
---
# <a name="tutorial-find-similar-images-from-previous-searches-using-an-image-insights-token"></a>Zelfstudie: vergelijkbare afbeeldingen uit eerdere zoekopdrachten zoeken met behulp van een afbeeldingsanalysetoken

Met de clientbibliotheek Visueel zoeken u afbeeldingen online `ImageInsightsToken`vinden uit eerdere zoekopdrachten die een. Deze toepassing `ImageInsightsToken` krijgt een en gebruikt het token in een volgende zoekopdracht. Het stuurt `ImageInsightsToken` vervolgens de naar Bing en retourneert resultaten die Bing Search URL's en URL's van soortgelijke afbeeldingen online gevonden bevatten.

De volledige broncode voor deze zelfstudie kan worden gevonden met extra foutafhandeling en annotaties op [GitHub.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInsightsTokens.cs)

## <a name="prerequisites"></a>Vereisten

* Elke editie van [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Als u Linux/MacOS gebruikt, u deze toepassing uitvoeren met [Mono.](https://www.mono-project.com/)
* De nuget visual search- en imagezoekpakketten.
    - Klik in de Solution Explorer in Visual Studio met de rechtermuisknop op uw project en selecteer **NuGet-pakketten beheren** in het menu. Installeer `Microsoft.Azure.CognitiveServices.Search.CustomSearch` het pakket `Microsoft.Azure.CognitiveServices.Search.ImageSearch` en het pakket. Als u de NuGet-pakketten installeert, worden ook de volgende onderdelen geïnstalleerd:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-client-library"></a>De ImageInsightsToken ophalen in de clientbibliotheek bing Image Search

Deze toepassing `ImageInsightsToken` maakt gebruik van een verkregen via de [Bing Image Search client bibliotheek](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart). Maak in een nieuwe C#-consoletoepassing een `ImageSearchClient()`client om de API aan te roepen met behulp van . Gebruik `SearchAsync()` dan met uw query:

```csharp
var client = new ImageSearchClient(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

Sla het eerste `imageResults.Value.First()`zoekresultaat op met behulp `ImageInsightsToken`van , en sla vervolgens de afbeelding inzicht's .

```csharp
String insightTok = "None";
if (imageResults.Value.Count > 0)
{
    var firstImageResult = imageResults.Value.First();
    insightTok = firstImageResult.ImageInsightsToken;
}
else
{
    insightTok = "None found";
    Console.WriteLine("Couldn't find image results!");
}
```

Dit `ImageInsightsToken` wordt verzonden naar Bing Visual Search in een aanvraag.

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>ImageInsightsToken toevoegen aan een visual search-aanvraag

Geef `ImageInsightsToken` het voor een visueel `ImageInfo` zoekverzoek `ImageInsightsToken` op door een object te maken uit de antwoorden van Bing Visual Search.

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>Bing Visual Search gebruiken om afbeeldingen van een ImageInsightsToken te vinden

Het `VisualSearchRequest` object bevat informatie `ImageInfo` over de afbeelding in te zoeken. Met de `VisualSearchMethodAsync()`-methode worden de resultaten opgehaald. U hoeft geen binaire afbeelding te verstrekken, omdat de afbeelding wordt weergegeven door het token.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>Herhalen door de visuele zoekresultaten

Visual Search-resultaten zijn `ImageTag`-objecten. Elke tag bevat een lijst met `ImageAction`-objecten. Elk `ImageAction` bevat `Data` een veld, dat is een lijst van waarden die afhankelijk zijn van het type actie. U herhalen door `ImageTag` de `visualSearchResults.Tags`objecten in, bijvoorbeeld, en krijg de `ImageAction` tag erin. In het onderstaande `PagesIncluding` voorbeeld worden de details van de acties afgedrukt:

```csharp
if (visualSearchResults.Tags.Count > 0)
{
    // List of tags
    foreach (ImageTag t in visualSearchResults.Tags)
    {
        foreach (ImageAction i in t.Actions)
        {
            Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " WebSearchURL: " + i.WebSearchUrl);

            if (i.ActionType == "PagesIncluding")
            {
                foreach (ImageObject o in (i as ImageModuleAction).Data.Value)
                {
                    Console.WriteLine("ContentURL: " + o.ContentUrl);
                }
            }
        }
    }
}
```

### <a name="pagesincluding-actiontypes"></a>Pagina's inclusief actietypen

Voor het ophalen van de werkelijke afbeeldings-URL's uit actietypen is een cast nodig die een `ActionType` as `ImageModuleAction`, die een `Data` element met een lijst met waarden bevat. Elke waarde is de URL van een afbeelding.  Met het volgende `PagesIncluding` wordt `ImageModuleAction` het actietype naar en leest u de waarden:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

Zie [Afbeeldingen - Visual Search](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) voor meer informatie over deze gegevenstypen.

## <a name="returned-urls"></a>Geretourneerde URL's

De volledige toepassing retourneert de volgende URL's:

|ActionType  |URL  | |
|---------|---------|---------|
|MoreSizes -> WebSearchUrl     |         |
|VisualSearch -> WebSearchUrl     |         |
|ImageById -> WebSearchUrl    |         |
|RelatedSearches -> WebSearchUrl:    |         |
|DocumentLevelSuggestions -> WebSearchUrl:     |         |
|TopicResults -> WebSearchUrl    | https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1 Geh7RucVvqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1       |
|ImageResults -> WebSearchUrl    |  https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE 3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1       |

Zoals hierboven wordt `TopicResults` `ImageResults` weergegeven, bevatten de typen query's voor gerelateerde afbeeldingen. De koppeling URL's naar de zoekresultaten van Bing.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app voor visueel zoeken met één pagina maken](tutorial-bing-visual-search-single-page-app.md)
