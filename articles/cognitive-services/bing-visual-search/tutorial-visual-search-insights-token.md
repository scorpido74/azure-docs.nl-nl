---
title: Zoek vergelijk bare afbeeldingen van de vorige Zoek opdrachten met behulp van image Insights-tokens en de Bing Visual Search-API
titleSuffix: Azure Cognitive Services
description: Gebruik de Bing Visual Search-client bibliotheek om Url's van afbeeldingen van vorige Zoek opdrachten op te halen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: aahi
ms.openlocfilehash: ad24a8a194a11c3fd5f7f77ea8c52197d5438edc
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80477923"
---
# <a name="tutorial-find-similar-images-from-previous-searches-using-an-image-insights-token"></a>Zelf studie: vergelijk bare afbeeldingen van vorige Zoek opdrachten zoeken met behulp van een image Insights-token

Met de Visual Search-client bibliotheek kunt u online afbeeldingen vinden op basis van eerdere Zoek `ImageInsightsToken`opdrachten die een. Deze toepassing ontvangt een `ImageInsightsToken` en gebruikt het token in een volgende zoek opdracht. Vervolgens wordt het `ImageInsightsToken` naar Bing verzonden en worden er resultaten weer gegeven met Bing Search Url's en url's van vergelijk bare afbeeldingen die online zijn gevonden.

De volledige bron code voor deze zelf studie vindt u in aanvullende fout afhandeling en annotaties op [github](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInsightsTokens.cs).

## <a name="prerequisites"></a>Vereisten

* Alle edities van [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Als u Linux/MacOS gebruikt, kunt u deze toepassing uitvoeren met [mono](https://www.mono-project.com/).
* De NuGet-Visual Search en Afbeeldingen zoeken-pakketten.
    - Klik in het Solution Explorer in Visual Studio met de rechter muisknop op uw project en selecteer **NuGet-pakketten beheren** in het menu. Installeer het `Microsoft.Azure.CognitiveServices.Search.CustomSearch` pakket en het `Microsoft.Azure.CognitiveServices.Search.ImageSearch` pakket. Als u de NuGet-pakketten installeert, worden ook de volgende onderdelen geïnstalleerd:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-client-library"></a>De ImageInsightsToken ophalen uit de Bing Image Search-client bibliotheek

Deze toepassing maakt gebruik `ImageInsightsToken` van een verkregen via de [Bing Image Search-client bibliotheek](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart). Maak in een nieuwe C#-console toepassing een client om de API aan te `ImageSearchClient()`roepen met. Vervolgens gebruiken `SearchAsync()` met uw query:

```csharp
var client = new ImageSearchClient(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

Sla het eerste Zoek resultaat op `imageResults.Value.First()`met en sla de afbeeldings inzichten op `ImageInsightsToken`.

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

Dit `ImageInsightsToken` wordt naar Bing Visual Search in een aanvraag verzonden.

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>De ImageInsightsToken toevoegen aan een Visual Search aanvraag

Geef de `ImageInsightsToken` aanvraag voor een Visual Search op door een `ImageInfo` object te maken `ImageInsightsToken` op basis van de antwoorden op de Bing Visual Search.

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>Bing Visual Search gebruiken om afbeeldingen van een ImageInsightsToken te zoeken

Het `VisualSearchRequest` object bevat informatie over de afbeelding in `ImageInfo` waarnaar moet worden gezocht. Met de `VisualSearchMethodAsync()`-methode worden de resultaten opgehaald. U hoeft geen binaire installatie kopie op te geven, omdat de afbeelding door het token wordt vertegenwoordigd.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>De Visual Search resultaten door lopen

Visual Search-resultaten zijn `ImageTag`-objecten. Elke tag bevat een lijst met `ImageAction`-objecten. Elk `ImageAction` bevat een `Data` veld. Dit is een lijst met waarden die afhankelijk zijn van het type actie. U kunt de `ImageTag` objecten in `visualSearchResults.Tags`, bijvoorbeeld door lopen en de `ImageAction` tag hierin ophalen. In het onderstaande voor beeld worden de `PagesIncluding` Details van de acties afgedrukt:

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

### <a name="pagesincluding-actiontypes"></a>PagesIncluding ActionTypes

Bij het ophalen van de daad werkelijke afbeeldings-Url's van actie typen `ActionType` is `ImageModuleAction`een cast-conversie `Data` vereist die een as bevat met een-element met een lijst met waarden. Elke waarde is de URL van een afbeelding.  Hieronder wordt het `PagesIncluding` actie type gecast naar `ImageModuleAction` en worden de waarden gelezen:

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

## <a name="returned-urls"></a>Geretourneerde Url's

De volledige toepassing retourneert de volgende Url's:

|ActionType  |URL  | |
|---------|---------|---------|
|MoreSizes-> WebSearchUrl     |         |
|VisualSearch-> WebSearchUrl     |         |
|ImageById-> WebSearchUrl    |         |
|RelatedSearches-> WebSearchUrl:    |         |
|DocumentLevelSuggestions-> WebSearchUrl:     |         |
|TopicResults-> WebSearchUrl    | https:\//www.Bing.com/CR?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&Cid = 1BA795A21EAF6A63175699B71FC36B7C&RD = 1&h = BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v = 1&r = https %3 a %2 f %2 f www. Bing. com% 2fdiscover% 2fcanadian% 2brocky&p = DevEx, 5823.1       |
|ImageResults-> WebSearchUrl    |  https:\//www.Bing.com/CR?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&Cid = 1BA795A21EAF6A63175699B71FC36B7C&RD = 1&h = PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v = 1&r = https %3 a %2 f %2 f www. Bing. com% 2fimages% 2fsearch% 3fq% 3doutdoor&p = DevEx, 5831.1       |

Zoals hierboven wordt weer gegeven `TopicResults` , `ImageResults` bevatten de typen en query's voor gerelateerde installatie kopieën. De Url's zijn gekoppeld aan Bing-Zoek resultaten.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een Visual Search Web-app met één pagina maken](tutorial-bing-visual-search-single-page-app.md)
