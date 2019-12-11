---
title: 'Snelstartgids: uw Bing Aangepaste zoekopdrachten-eind punt aanroepen met de C# SDK | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Gebruik deze Quick Start om te beginnen met het aanvragen van zoek resultaten van C# uw Bing aangepaste zoekopdrachten-exemplaar met behulp van de SDK.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: scottwhi
ms.openlocfilehash: 21b192d29f30d66e3d5f0ce6e04317d3ca7c860e
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977145"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-c-sdk"></a>Snelstartgids: uw Bing Aangepaste zoekopdrachten-eind punt aanroepen met de C# SDK 

Gebruik deze quickstart om te beginnen met het opvragen van zoekresultaten van uw Bing Custom Search-instantie met behulp van de C# SDK. Hoewel Bing Custom Search een REST-API heeft die compatibel is met de meeste programmeertalen, biedt de Bing Custom Search SDK een eenvoudige manier om de service in uw toepassingen te integreren. De broncode voor dit voorbeeld is te vinden [op GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch).

## <a name="prerequisites"></a>Vereisten

- Een Bing Custom Search-exemplaar. Zie [Quick Start: uw eerste Bing aangepaste zoekopdrachten-exemplaar maken](quick-start.md) voor meer informatie.
- Microsoft [.NET Core](https://www.microsoft.com/net/download/core)
- Elke versie van [Visual Studio 2017 of hoger](https://www.visualstudio.com/downloads/)
- Als u Linux/MacOS gebruikt, kan deze toepassing worden uitgevoerd met behulp van [Mono](https://www.mono-project.com/).
- Het NuGet-pakket van [Bing aangepaste zoekopdrachten](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) . 
    - Klik vanuit **Solution Explorer** in Visual Studio met de rechter muisknop op het project en selecteer **NuGet-pakketten beheren** in het menu. Installeer het `Microsoft.Azure.CognitiveServices.Search.CustomSearch`-pakket. Met de installatie van het NuGet-pakket Custom Search worden ook de volgende assembly's geïnstalleerd:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-news-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


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
    ```csharp

## Next steps

> [!div class="nextstepaction"]
> [Build a Custom Search web app](./tutorials/custom-search-web-page.md)
