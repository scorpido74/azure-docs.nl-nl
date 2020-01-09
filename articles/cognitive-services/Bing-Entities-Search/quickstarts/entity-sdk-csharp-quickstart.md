---
title: 'Snelstartgids: zoeken naar entiteiten met de SDK voor C# -Bing entity Search'
titleSuffix: Azure Cognitive Services
description: Gebruik deze quickstart om te zoeken naar entiteiten met de Bing Entity Search SDK voor C#.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 12/11/2019
ms.author: aahi
ms.openlocfilehash: 4c942040a36ae7b103f7dabac62376ea5a4e2890
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75384533"
---
# <a name="send-a-search-request-with-the-bing-entity-search-sdk-for-c"></a>Een aanvraag verzenden om te zoeken naar entiteiten met de Bing Entity Search SDK voor C#

Gebruik deze quickstart om te zoeken naar entiteiten met de Bing Entity Search SDK voor C#. Hoewel Bing Entiteiten zoeken een REST-API heeft die compatibel is met de meeste moderne programmeertalen, biedt de SDK een eenvoudige manier om de service te integreren in uw toepassingen. De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingEntitySearch).


## <a name="prerequisites"></a>Vereisten

* Een versie van [Visual Studio 2017 of hoger](https://www.visualstudio.com/downloads/).
* Het [Json.NET](https://www.newtonsoft.com/json)-framework, beschikbaar als NuGet-pakket.
* Als u Linux/MacOS gebruikt, kan deze toepassing worden uitgevoerd met behulp van [Mono](https://www.mono-project.com/).
* Het [NuGet-pakket voor de Bing Nieuws zoeken SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.EntitySearch/1.2.0). Als u dit pakket installeert, worden ook de volgende onderdelen geïnstalleerd:
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

Als u de Bing Entity Search SDK wilt toevoegen aan uw Visual Studio-project, gebruikt u de optie **NuGet packages beheren** van **Solution Explorer**en voegt u het `Microsoft.Azure.CognitiveServices.Search.EntitySearch` pakket toe.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>De toepassing maken en initialiseren

1. Maak een nieuwe C#-console-oplossing in Visual Studio. Voeg vervolgens de volgende code in het hoofdcodebestand in.

    ```csharp
    using System;
    using System.Linq;
    using System.Text;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch.Models;
    using Newtonsoft.Json;
    ```

## <a name="create-a-client-and-send-a-search-request"></a>Een client aanmaken en een zoekaanvraag verzenden

1. Maak een nieuwe zoekclient aan. Uw abonnementssleutel toevoegen door het maken van een nieuw `ApiKeyServiceClientCredentials`.

    ```csharp
    var client = new EntitySearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

1. Gebruik de functie `Entities.Search()` van de client om te zoeken naar uw query:
    
    ```csharp
    var entityData = client.Entities.Search(query: "Satya Nadella");
    ```

## <a name="get-and-print-an-entity-description"></a>Een beschrijving van een entiteit ophalen en afdrukken

1. Als de API zoekresultaten retourneert, haalt u de belangrijkste entiteit op basis van `entityData` op.

    ```csharp
    var mainEntity = entityData.Entities.Value.Where(thing => thing.EntityPresentationInfo.EntityScenario == EntityScenario.DominantEntity).FirstOrDefault();
    ```

2. De beschrijving van de belangrijkste entiteit afdrukken 

    ```csharp
    Console.WriteLine(mainEntity.Description);
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app met één pagina maken](../tutorial-bing-entities-search-single-page-app.md)

* [Wat is de Bing Entiteiten zoeken-API?](../overview.md )