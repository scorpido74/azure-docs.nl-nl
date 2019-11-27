---
title: 'Snelstartgids: Image Insights ophalen met de SDK voor C# -Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Leer hoe u een afbeelding uploadt met behulp van de Bing Visual Search SDK en inzichten in de afbeelding verkrijgt.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 3db40f999770823052f07a328d3d8dc8c01ce345
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383579"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-c"></a>Snelstartgids: Image Insights ophalen met behulp van de Bing Visual Search SDK voorC#

In deze quickstart leert u hoe u met de C#-SDK afbeeldingsinzichten krijgt uit de Bing Visual Search-service. Hoewel Bing Visual Search een REST API heeft die compatibel is met de meeste programmeertalen, biedt de SDK een eenvoudige manier om de service in uw toepassingen te integreren. De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVisualSearch).

## <a name="prerequisites"></a>Vereisten

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Als u Linux/MacOS gebruikt, kan deze toepassing worden uitgevoerd met behulp van [Mono](https://www.mono-project.com/).
* Het NuGet Visual Search-pakket. 
    - Klik in Solution Explorer in Visual Studio met de rechtermuisknop op uw project en selecteer `Manage NuGet Packages` in het menu. Installeer het `Microsoft.Azure.CognitiveServices.Search.VisualSearch`-pakket. Als u de NuGet-pakketten installeert, worden ook de volgende onderdelen geïnstalleerd:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak een nieuw project in Visual Studio. Voeg vervolgens de volgende instructies toe.
    
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
    ```

2. Maak een instantie van de client met behulp van uw abonnementssleutel.
    
    ```csharp
    var client = new VisualSearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```
    
## <a name="send-a-search-request"></a>Een zoekaanvraag verzenden 

1. Maak een `FileStream` naar de afbeeldingen (in dit geval `TestImages/image.jpg`). Gebruik vervolgens de client om een zoekaanvraag te verzenden met `client.Images.VisualSearchMethodAsync()`. 
    
    ```csharp
     System.IO.FileStream stream = new FileStream(Path.Combine("TestImages", "image.jpg"), FileMode.Open);
     // The knowledgeRequest parameter is not required if an image binary is passed in the request body
     var visualSearchResults = client.Images.VisualSearchMethodAsync(image: stream, knowledgeRequest: (string)null).Result;
    ```
    
2. De resultaten van de vorige query parseren:

    ```csharp
    // Visual Search results
    if (visualSearchResults.Image?.ImageInsightsToken != null)
    {
        Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
    }
    else
    {
        Console.WriteLine("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.Tags.Count > 0)
    {
        var firstTagResult = visualSearchResults.Tags[0];
        Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");
    
        // List of actions in first tag
        if (firstTagResult.Actions.Count > 0)
        {
            var firstActionResult = firstTagResult.Actions[0];
            Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
            Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
        }
        else
        {
            Console.WriteLine("Couldn't find tag actions!");
        }
    }
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app van één pagina maken](tutorial-bing-visual-search-single-page-app.md).
