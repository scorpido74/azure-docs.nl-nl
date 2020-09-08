---
title: Quickstart voor Bing Entity Search-clientbibliotheek voor C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: 27951a6da2c5e4a9d17e8b332d8ee9706701649d
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88931881"
---
Gebruik deze quickstart om te zoeken naar entiteiten met de Bing Entity Search-clientbibliotheek voor C#. Hoewel Bing Entity Search een REST API heeft die compatibel is met de meeste programmeertalen, biedt de clientbibliotheek een eenvoudige manier om de service in uw toepassingen te integreren. De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingEntitySearch).


## <a name="prerequisites"></a>Vereisten

* Een versie van [Visual Studio 2017 of hoger](https://www.visualstudio.com/downloads/).
* Het [Json.NET](https://www.newtonsoft.com/json)-framework, beschikbaar als NuGet-pakket.
* Als u Linux/MacOS gebruikt, kan deze toepassing worden uitgevoerd met behulp van [Mono](https://www.mono-project.com/).
* Het [NuGet-pakket voor de Bing Nieuws zoeken SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.EntitySearch/1.2.0). Als u dit pakket installeert, worden ook de volgende onderdelen geïnstalleerd:
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

Als u de Bing Entity Search-clientbibliotheek aan uw Visual Studio-project wilt toevoegen, gebruikt u de optie **NuGet-pakketten beheren** van **Solution Explorer** en voegt u het pakket `Microsoft.Azure.CognitiveServices.Search.EntitySearch` toe.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]

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
> [Een web-app met één pagina maken](../../tutorial-bing-entities-search-single-page-app.md)

* [Wat is de Bing Entiteiten zoeken-API?](../../overview.md )
