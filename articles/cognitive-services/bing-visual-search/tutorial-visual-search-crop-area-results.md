---
title: 'Zelfstudie: Een afbeelding bijsnijden met de Bing Visual Search SDK'
description: Gebruik de Bing Visual Search SDK om inzichten te krijgen uit specifieke ares op een afbeelding.
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74689353"
---
# <a name="tutorial-crop-an-image-with-the-bing-visual-search-sdk-for-c"></a>Zelfstudie: Een afbeelding bijsnijden met de Bing Visual Search SDK voor C #

Met de Bing Visual Search SDK u een afbeelding bijsnijden voordat u vergelijkbare online afbeeldingen vindt. Deze toepassing snijdt één persoon op uit een afbeelding met meerdere personen en retourneert vervolgens zoekresultaten met vergelijkbare afbeeldingen die online zijn gevonden.

De volledige broncode voor deze toepassing is beschikbaar met extra foutafhandeling en annotaties op [GitHub.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchCropImage.cs)

Deze zelfstudie illustreert hoe u:

> [!div class="checklist"]
> * Een aanvraag verzenden met de Bing Visual Search SDK
> * Een afbeeldingsgebied bijsnijden om te zoeken met Bing Visual Search
> * Het antwoord ontvangen en verwerken
> * De URL's van actie-items zoeken in het antwoord

## <a name="prerequisites"></a>Vereisten

* Elke editie van [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Als u Linux/MacOS gebruikt, kan deze toepassing worden uitgevoerd met behulp van [Mono](https://www.mono-project.com/).
* Het [NuGet-pakket Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) is geïnstalleerd.
    - Klik in de Solution Explorer in Visual Studio met de rechtermuisknop op uw project en selecteer **NuGet-pakketten beheren** in het menu. Installeer het `Microsoft.Azure.CognitiveServices.Search.CustomSearch`-pakket. Met de installatie van het NuGet-pakket Custom Search worden ook de volgende assembly's geïnstalleerd:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="specify-the-image-crop-area"></a>Het bijsnijdgebied van de afbeelding opgeven

Deze toepassing gewassen een gebied van dit beeld van de Microsoft senior leadership team. Dit gewasgebied wordt gedefinieerd met behulp van coördinaten linksboven en rechtsonder, vertegenwoordigd als een percentage van de hele afbeelding:  

![Senior leidinggevenden bij Microsoft](./media/MS_SrLeaders.jpg)

Deze afbeelding wordt bijgesneden `ImageInfo` door een object uit `ImageInfo` het bijsnijdgebied te maken en het object te laden in een `VisualSearchRequest`. Het `ImageInfo` object bevat ook de URL van de afbeelding:

```csharp
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```

## <a name="search-for-images-similar-to-the-crop-area"></a>Zoeken naar afbeeldingen die vergelijkbaar zijn met het bijsnijdgebied

De `VisualSearchRequest` variabele bevat informatie over het bijsnijdgebied van de afbeelding en de URL. De `VisualSearchMethodAsync()` methode krijgt de resultaten:

```csharp
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result;

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>De URL-gegevens ophalen van`ImageModuleAction`

Visuele zoekresultaten van `ImageTag` Bing zijn objecten. Elke tag bevat een lijst met `ImageAction`-objecten. Elk `ImageAction` bevat `Data` een veld, dat is een lijst van waarden die afhankelijk zijn van het type actie.

U de verschillende typen afdrukken met de volgende code:

```csharp
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);
```

De volledige toepassing retourneert:

|ActionType  |URL  | |
|---------|---------|---------|
|Pagina's met websearchURL     |         |
|MoreSizes WebSearchURL     |         |  
|VisualSearch WebSearchURL    |         |
|ImageById WebSearchURL     |         |  
|RelatedSearches WebSearchURL     |         |
|Entiteit > WebSearchUrl     | https\:/www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=&BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1        |
|TopicResults -> WebSearchUrl    |  https\:]www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPk UYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1        |
|ImageResults -> WebSearchUrl    |  https\:]www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJsz goum5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1        |

Zoals hierboven wordt `Entity` weergegeven, bevat het ActionType een Bing-zoekopdracht die informatie retourneert over een herkenbare persoon, plaats of ding. De typen `TopicResults` en `ImageResults` bevatten zoekopdrachten naar gerelateerde afbeeldingen. De URL's in de lijst zijn koppelingen naar Bing-zoekresultaten.

## <a name="get-urls-for-pagesincluding-actiontype-images"></a>URL's `PagesIncluding` `ActionType` voor afbeeldingen

Voor het ophalen van de werkelijke afbeeldings-URL's is een cast vereist die een `ActionType` leest als `ImageModuleAction`, die een `Data`-element met een waardelijst bevat. Elke waarde is de URL van een afbeelding. Met het volgende `PagesIncluding` wordt `ImageModuleAction` het actietype naar en leest u de waarden:

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
> [Een web-app voor visueel zoeken met één pagina maken](tutorial-bing-visual-search-single-page-app.md)

## <a name="see-also"></a>Zie ook
> [Wat is Bing Visual Search-API?](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview)
