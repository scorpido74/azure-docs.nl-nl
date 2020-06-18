---
title: Quick start voor Bing News Search C#-client bibliotheek
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.openlocfilehash: fbc7d7b35b46e96e9cd007a3794a8ccc561f91e4
ms.sourcegitcommit: 1383842d1ea4044e1e90bd3ca8a7dc9f1b439a54
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/16/2020
ms.locfileid: "84878954"
---
Gebruik deze Quick Start om te beginnen met zoeken naar nieuws met de Bing News Search-client bibliotheek voor C#. Hoewel Bing News Search een REST API compatibel met de meeste programmeer talen heeft, biedt de client bibliotheek een eenvoudige manier om de service te integreren in uw toepassingen. De bron code voor dit voor beeld is te vinden op [github](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingNewsSearch).

## <a name="prerequisites"></a>Vereisten

* Een versie van [Visual Studio 2017 of hoger](https://www.visualstudio.com/downloads/).
* Het [JSON.net](https://www.newtonsoft.com/json) -Framework, beschikbaar als een NuGet-pakket.
* Als u Linux/MacOS gebruikt, kan deze toepassing worden uitgevoerd met behulp van [Mono](https://www.mono-project.com/).

* Het [NuGet-pakket voor de Bing Nieuws zoeken SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/2.0.0). Als u dit pakket installeert, worden ook de volgende onderdelen geïnstalleerd:
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

Als u een console toepassing wilt instellen met behulp van de Bing News Search-client bibliotheek, bladert u naar de `Manage NuGet Packages` optie in de Solution Explorer in Visual Studio.  Voeg het pakket `Microsoft.Azure.CognitiveServices.Search.NewsSearch` toe.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Een project maken en initialiseren

1. Maak een nieuwe C#-console-oplossing in Visual Studio. Voeg vervolgens de volgende code in het hoofdcodebestand in.
    
    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch;
    ```

2. Maak een variabele voor uw API-sleutel en een zoekterm en maak hiermee daarna een exemplaar van de client voor Nieuws zoeken.

    ```csharp
    var key = "YOUR-ACCESS-KEY";
    var searchTerm = "Quantum Computing";
    var client = new NewsSearchClient(new ApiKeyServiceClientCredentials(key));
    ```

## <a name="send-a-request-and-parse-the-result"></a>Een aanvraag verzenden en het resultaat parseren

1. Gebruik de client om een zoekaanvraag te verzenden naar de service voor Bing Nieuws zoeken:
    ```csharp
    var newsResults = client.News.SearchAsync(query: searchTerm, market: "en-us", count: 10).Result;
    ```

2. Als er resultaten worden geretourneerd, parseert u deze:

    ```csharp
    if (newsResults.Value.Count > 0)
    {
        var firstNewsResult = newsResults.Value[0];
    
        Console.WriteLine($"TotalEstimatedMatches value: {newsResults.TotalEstimatedMatches}");
        Console.WriteLine($"News result count: {newsResults.Value.Count}");
        Console.WriteLine($"First news name: {firstNewsResult.Name}");
        Console.WriteLine($"First news url: {firstNewsResult.Url}");
        Console.WriteLine($"First news description: {firstNewsResult.Description}");
        Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
        Console.WriteLine($"First news provider: {firstNewsResult.Provider[0].Name}");
    }
    
    else
    {
        Console.WriteLine("Couldn't find news results!");
    }
    Console.WriteLine("Enter any key to exit...");
    Console.ReadKey();
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app met één pagina maken](../../tutorial-bing-news-search-single-page-app.md)
