---
title: "Snelstartgids: zoeken naar Video's met de SDK voor C# -Bing Video Search"
titleSuffix: Azure Cognitive Services
description: Gebruik deze quickstart om zoekaanvragen voor video's te verzenden naar de Bing Video's zoeken-SDK voor C#.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 06/26/2019
ms.author: aahi
ms.openlocfilehash: d29aef614b8308dfeba8da7925bd2880c25fe72d
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383763"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-c"></a>Quick Start: een video zoekopdracht uitvoeren met de Bing Video Search SDK voorC#

Gebruik deze quickstart om aan de slag te gaan met de Bing Video's zoeken-SDK voor C# om nieuws te zoeken. Hoewel Bing Video's zoeken een REST API heeft die compatibel is met de meeste programmeertalen, biedt de SDK een eenvoudige manier om de service in uw toepassingen te integreren. De bron code voor dit voor beeld is te vinden op [github](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch) met aanvullende aantekeningen en functies.

## <a name="prerequisites"></a>Vereisten

* Een versie van [Visual Studio 2017 of hoger](https://visualstudio.microsoft.com/downloads/).
* Het Json.NET-framework, beschikbaar als [NuGet-pakket](https://www.nuget.org/packages/Newtonsoft.Json/).

Als u de Bing Video Search SDK wilt toevoegen aan uw project, selecteert u **NuGet-pakketten beheren** in **Solution Explorer** in Visual Studio. Voeg het pakket `Microsoft.Azure.CognitiveServices.Search.VideoSearch` toe.

Wanneer u het [[NuGet Video Search SDK-pakket]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0) installeert, worden ook de volgende afhankelijkheden geïnstalleerd:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Een project maken en initialiseren

1. Maak een nieuwe C#-console-oplossing in Visual Studio. Voeg vervolgens de volgende code in het hoofdcodebestand in.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;
    ```

2. Start de client door een nieuw `ApiKeyServiceClientCredentials`-object met uw abonnementssleutel te maken en roep de constructor aan.

    ```csharp
    var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

## <a name="send-a-search-request-and-process-the-results"></a>Een zoekopdracht verzenden en de resultaten verwerken

1. Gebruik de client om een zoekopdracht te verzenden. Gebruik 'SwiftKey' voor de zoekquery.

    ```csharp
    var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
    ```

2. Als er resultaten worden geretourneerd, krijgt u de eerste met `videoResults.Value[0]`. Druk vervolgens de id, de titel en de url van de video af.

    ```csharp
    if (videoResults.Value.Count > 0)
    {
        var firstVideoResult = videoResults.Value[0];

        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
        Console.WriteLine($"First video name: {firstVideoResult.Name}");
        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
    }
    else
    {
        Console.WriteLine("Couldn't find video results!");
    }
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app met één pagina maken](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Zie ook 

* [Wat is de Bing Video's zoeken-API?](../overview.md)
* [Voorbeelden voor Cognitive Services .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
