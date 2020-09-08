---
title: Quickstart voor Bing Video Search-clientbibliotheek voor C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 6d50a8e2c9d0263616b25e25958be6a6f0fb7fe1
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88929263"
---
Gebruik deze quickstart om aan de slag te gaan met de Bing Video Search-clientbibliotheek voor C# om nieuws te zoeken. Hoewel Bing Video Search een REST API heeft die compatibel is met de meeste programmeertalen, biedt de clientbibliotheek een eenvoudige manier om de service in uw toepassingen te integreren. De broncode voor dit voorbeeld is beschikbaar op [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch) met extra aantekeningen en functies.

## <a name="prerequisites"></a>Vereisten

* Een versie van [Visual Studio 2017 of hoger](https://visualstudio.microsoft.com/downloads/).
* Het Json.NET-framework, beschikbaar als [NuGet-pakket](https://www.nuget.org/packages/Newtonsoft.Json/).

Als u de Bing Video Search-clientbibliotheek wilt toevoegen aan uw project, selecteert u **NuGet-pakketten beheren** vanuit **Solution Explorer** in Visual Studio. Voeg het pakket `Microsoft.Azure.CognitiveServices.Search.VideoSearch` toe.

Wanneer u het [[NuGet Video Search SDK-pakket]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0) installeert, worden ook de volgende afhankelijkheden geïnstalleerd:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]


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
> [Een web-app met één pagina maken](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Zie ook 

* [Wat is de Bing Video's zoeken-API?](../../overview.md)
* [Voorbeelden voor Cognitive Services .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
