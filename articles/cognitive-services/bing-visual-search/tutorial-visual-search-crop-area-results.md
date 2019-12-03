---
title: 'Zelf studie: een afbeelding bijsnijden met de Bing Visual Search SDK'
description: Gebruik de Bing Visual Search SDK om inzichten te verkrijgen op basis van specifieke aren van een afbeelding.
services: cognitive-services
titleSuffix: Azure Cognitive Services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: 7adca44f1710431ad1095cbd0da897d4c7c7f325
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689353"
---
# <a name="tutorial-crop-an-image-with-the-bing-visual-search-sdk-for-c"></a>Zelf studie: een afbeelding bijsnijden met de Bing Visual Search SDK voorC#

Met de Bing Visual Search SDK kunt u een afbeelding bijsnijden voordat u vergelijk bare online afbeeldingen kunt vinden. Met deze toepassing wordt één persoon bijgesneden van een afbeelding met meerdere personen, waarna Zoek resultaten worden geretourneerd met vergelijk bare afbeeldingen die online zijn gevonden.

De volledige bron code voor deze toepassing is beschikbaar met aanvullende fout afhandeling en annotaties op [github](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchCropImage.cs).

In deze zelf studie ziet u hoe u:

> [!div class="checklist"]
> * Een aanvraag verzenden met behulp van de Bing Visual Search SDK
> * Een gebied met een afbeelding bijsnijden om met Bing Visual Search te zoeken
> * Het antwoord ontvangen en verwerken
> * De Url's van actie-items in het antwoord zoeken

## <a name="prerequisites"></a>Vereisten

* Alle edities van [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Als u Linux/MacOS gebruikt, kan deze toepassing worden uitgevoerd met behulp van [Mono](https://www.mono-project.com/).
* Het [NuGet-pakket Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) is geïnstalleerd.
    - Klik in het Solution Explorer in Visual Studio met de rechter muisknop op uw project en selecteer **NuGet-pakketten beheren** in het menu. Installeer het `Microsoft.Azure.CognitiveServices.Search.CustomSearch`-pakket. Met de installatie van het NuGet-pakket Custom Search worden ook de volgende assembly's geïnstalleerd:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="specify-the-image-crop-area"></a>Het snij gebied voor de afbeelding opgeven

Met deze toepassing wordt een gebied van deze afbeelding van het team van micro soft senior leiderschap bijgesneden. Dit snij gebied is gedefinieerd met de linker-en rechter coördinaat, uitgedrukt als een percentage van de hele afbeelding:  

![Senior leidinggevenden bij Microsoft](./media/MS_SrLeaders.jpg)

Deze afbeelding wordt bijgesneden door een `ImageInfo`-object te maken op basis van het snij gebied en het `ImageInfo` object in een `VisualSearchRequest`te laden. Het `ImageInfo`-object bevat ook de URL van de afbeelding:

```csharp
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```

## <a name="search-for-images-similar-to-the-crop-area"></a>Zoeken naar afbeeldingen die vergelijkbaar zijn met het snij gebied

De variabele `VisualSearchRequest` bevat informatie over het snij gebied van de afbeelding en de bijbehorende URL. De resultaten van de `VisualSearchMethodAsync()` methode worden opgehaald:

```csharp
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result;

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>De URL-gegevens ophalen van `ImageModuleAction`

Bing Visual Search resultaten zijn `ImageTag`-objecten. Elke tag bevat een lijst met `ImageAction`-objecten. Elk `ImageAction` bevat een `Data` veld, een lijst met waarden die afhankelijk zijn van het type actie.

U kunt de verschillende typen afdrukken met de volgende code:

```csharp
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);
```

De volledige toepassing retourneert:

|Soort  |URL  | |
|---------|---------|---------|
|PagesIncluding WebSearchURL     |         |
|MoreSizes WebSearchURL     |         |  
|VisualSearch WebSearchURL    |         |
|ImageById WebSearchURL     |         |  
|RelatedSearches WebSearchURL     |         |
|Entiteit-> WebSearchUrl     | https-\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1        |
|TopicResults-> WebSearchUrl    |  https-\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1        |
|ImageResults-> WebSearchUrl    |  https-\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1        |

Zoals hierboven wordt weer gegeven, bevat de `Entity` action type een Bing-Zoek query die informatie over een herken bare persoon, plaats of ding retourneert. De typen `TopicResults` en `ImageResults` bevatten zoekopdrachten naar gerelateerde afbeeldingen. De URL's in de lijst zijn koppelingen naar Bing-zoekresultaten.

## <a name="get-urls-for-pagesincluding-actiontype-images"></a>Url's voor `PagesIncluding` `ActionType`-installatie kopieën ophalen

Voor het ophalen van de werkelijke afbeeldings-URL's is een cast vereist die een `ActionType` leest als `ImageModuleAction`, die een `Data`-element met een waardelijst bevat. Elke waarde is de URL van een afbeelding. Hieronder wordt het `PagesIncluding` actie type gecast naar `ImageModuleAction` en worden de waarden gelezen:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Een Visual Search Web-app met één pagina maken](tutorial-bing-visual-search-single-page-app.md)

## <a name="see-also"></a>Zie ook
> [Wat is de Bing Visual Search-API?](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview)
