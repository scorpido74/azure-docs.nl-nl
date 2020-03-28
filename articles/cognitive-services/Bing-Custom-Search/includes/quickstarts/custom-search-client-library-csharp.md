---
title: Bing Custom Search C# clientbibliotheek snel aan de slag
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: ba80d1396b30b61bdfe4c121220429f5a7d994b0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "79486148"
---
Ga aan de slag met de Bing Custom Search-clientbibliotheek voor C#. Volg deze stappen om het pakket te installeren en probeer de voorbeeldcode voor basistaken uit. Met de Bing Custom Search API u op maat gemaakte, advertentievrije zoekervaringen maken voor onderwerpen die u belangrijk vindt. De broncode voor dit voorbeeld is te vinden op [GitHub.](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch)

Gebruik de clientbibliotheek Bing Custom Search voor C# om:
* Zoek zoekresultaten op internet in uw instantie Bing Custom Search.

[Voorbeeld van naslagdocumentatie](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-dotnet) | [Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [(NuGet)-voorbeelden](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) | [Samples](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)


## <a name="prerequisites"></a>Vereisten

- Een Bing Custom Search-exemplaar. Zie [Snelstart: Maak uw eerste voorbeeld van Bing Custom Search](../../quick-start.md) voor meer informatie.
- Microsoft [.NET Core](https://www.microsoft.com/net/download/core)
- Elke editie van [Visual Studio 2017 of hoger](https://www.visualstudio.com/downloads/)
- Als u Linux/MacOS gebruikt, kan deze toepassing worden uitgevoerd met behulp van [Mono](https://www.mono-project.com/).
- Het [Bing Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) NuGet-pakket. 
    - Klik vanuit **Solution Explorer** in Visual Studio met de rechtermuisknop op uw project en selecteer **NuGet-pakketten beheren** in het menu. Installeer het `Microsoft.Azure.CognitiveServices.Search.CustomSearch`-pakket. Met de installatie van het NuGet-pakket Custom Search worden ook de volgende assembly's geïnstalleerd:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak in Visual Studio een nieuwe C#-consoletoepassing. Voeg vervolgens de volgende pakketten toe aan uw project.

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
    ```

2. Instantieer in de hoofdmethode van uw toepassing de zoekclient met uw API-sleutel.

    ```csharp
    var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));
    ```

## <a name="send-the-search-request-and-receive-a-response"></a>De zoekopdracht verzenden en een reactie ontvangen
    
1. Verzend een zoekopdracht met behulp van de `SearchAsync()`-methode van uw client, en sla het antwoord op. Vervang uw `YOUR-CUSTOM-CONFIG-ID` door de configuratie-id van uw exemplaar (u vindt de id in de [portal van Bing Custom Search](https://www.customsearch.ai/)). In dit voorbeeld wordt gezocht naar 'Xbox'.

    ```csharp
    // This will look up a single query (Xbox).
    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
    ```

2. De methode `SearchAsync()` retourneert een `WebData`-object. Gebruik het object om de gevonden `WebPages` te doorlopen. Met deze code vindt u het eerste resultaat dat uit een webpagina bestaat en worden de `Name` en `URL` van de webpagina weergegeven.

    ```csharp
    if (webData?.WebPages?.Value?.Count > 0)
    {
        // find the first web page
        var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

        if (firstWebPagesResult != null)
        {
            Console.WriteLine("Number of webpage results {0}", webData.WebPages.Value.Count);
            Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
            Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
        }
        else
        {
            Console.WriteLine("Couldn't find web results!");
        }
    }
    else
    {
        Console.WriteLine("Didn't see any Web data..");
    }
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app voor aangepaste zoekopdrachten bouwen](../../tutorials/custom-search-web-page.md)
